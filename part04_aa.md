    return find_links


def fetch_build_egg(dist, req):
    """Fetch an egg needed for building.

    Use pip/wheel to fetch/build a wheel."""
    _DeprecatedInstaller.emit()
    _warn_wheel_not_available(dist)
    return _fetch_build_egg_no_warn(dist, req)


def _present(req):
    return any(_dist_matches_req(dist, req) for dist in metadata.distributions())


def _fetch_build_eggs(dist, requires: _reqs._StrOrIter) -> list[metadata.Distribution]:
    _DeprecatedInstaller.emit(stacklevel=3)
    _warn_wheel_not_available(dist)

    parsed_reqs = _reqs.parse(requires)

    missing_reqs = itertools.filterfalse(_present, parsed_reqs)

    needed_reqs = (
        req for req in missing_reqs if not req.marker or req.marker.evaluate()
    )
    resolved_dists = [_fetch_build_egg_no_warn(dist, req) for req in needed_reqs]
    for dist in resolved_dists:
        # dist.locate_file('') is the directory containing EGG-INFO, where the importabl
        # contents can be found.
        sys.path.insert(0, str(dist.locate_file('')))
    return resolved_dists


def _dist_matches_req(egg_dist, req):
    return (
        packaging.utils.canonicalize_name(egg_dist.name)
        == packaging.utils.canonicalize_name(req.name)
        and egg_dist.version in req.specifier
    )


def _fetch_build_egg_no_warn(dist, req):  # noqa: C901  # is too complex (16)  # FIXME
    # Ignore environment markers; if supplied, it is required.
    req = strip_marker(req)
    # Take easy_install options into account, but do not override relevant
    # pip environment variables (like PIP_INDEX_URL or PIP_QUIET); they'll
    # take precedence.
    opts = dist.get_option_dict('easy_install')
    if 'allow_hosts' in opts:
        raise DistutilsError(
            'the `allow-hosts` option is not supported '
            'when using pip to install requirements.'
        )
    quiet = 'PIP_QUIET' not in os.environ and 'PIP_VERBOSE' not in os.environ
    if 'PIP_INDEX_URL' in os.environ:
        index_url = None
    elif 'index_url' in opts:
        index_url = opts['index_url'][1]
    else:
        index_url = None
    find_links = (
        _fixup_find_links(opts['find_links'][1])[:] if 'find_links' in opts else []
    )
    if dist.dependency_links:
        find_links.extend(dist.dependency_links)
    eggs_dir = os.path.realpath(dist.get_egg_cache_dir())
    cached_dists = metadata.Distribution.discover(path=glob.glob(f'{eggs_dir}/*.egg'))
    for egg_dist in cached_dists:
        if _dist_matches_req(egg_dist, req):
            return egg_dist
    with tempfile.TemporaryDirectory() as tmpdir:
        cmd = [
            sys.executable,
            '-m',
            'pip',
            '--disable-pip-version-check',
            'wheel',
            '--no-deps',
            '-w',
            tmpdir,
        ]
        if quiet:
            cmd.append('--quiet')
        if index_url is not None:
            cmd.extend(('--index-url', index_url))
        for link in find_links or []:
            cmd.extend(('--find-links', link))
        # If requirement is a PEP 508 direct URL, directly pass
        # the URL to pip, as `req @ url` does not work on the
        # command line.
        cmd.append(req.url or str(req))
        try:
            subprocess.check_call(cmd)
        except subprocess.CalledProcessError as e:
            raise DistutilsError(str(e)) from e
        wheel = Wheel(glob.glob(os.path.join(tmpdir, '*.whl'))[0])
        dist_location = os.path.join(eggs_dir, wheel.egg_name())
        wheel.install_as_egg(dist_location)
        return metadata.Distribution.at(dist_location + '/EGG-INFO')


def strip_marker(req):
    """
    Return a new requirement without the environment marker to avoid
    calling pip with something like `babel; extra == "i18n"`, which
    would always be ignored.
    """
    # create a copy to avoid mutating the input
    req = packaging.requirements.Requirement(str(req))
    req.marker = None
    return req


def _warn_wheel_not_available(dist):
    try:
        metadata.distribution('wheel')
    except metadata.PackageNotFoundError:
        dist.announce('WARNING: The wheel package is not available.', log.WARN)


class _DeprecatedInstaller(SetuptoolsDeprecationWarning):
    _SUMMARY = "setuptools.installer and fetch_build_eggs are deprecated."
    _DETAILS = """
    Requirements should be satisfied by a PEP 517 installer.
    If you are using pip, you can try `pip install --use-pep517`.
    """
    _DUE_DATE = 2025, 10, 31



## File: launch.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/launch.py`*

```python

"""
Launch the Python script on the command line after
setuptools is bootstrapped via import.
"""

# Note that setuptools gets imported implicitly by the
# invocation of this script using python -m setuptools.launch

import sys
import tokenize


def run() -> None:
    """
    Run the script in sys.argv[1] as if it had
    been invoked naturally.
    """
    __builtins__
    script_name = sys.argv[1]
    namespace = dict(
        __file__=script_name,
        __name__='__main__',
        __doc__=None,
    )
    sys.argv[:] = sys.argv[1:]

    open_ = getattr(tokenize, 'open', open)
    with open_(script_name) as fid:
        script = fid.read()
    norm_script = script.replace('\\r\\n', '\\n')
    code = compile(norm_script, script_name, 'exec')
    exec(code, namespace)


if __name__ == '__main__':
    run()



## File: logging.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/logging.py`*

```python

import inspect
import logging
import sys

from . import monkey

import distutils.log


def _not_warning(record):
    return record.levelno < logging.WARNING


def configure() -> None:
    """
    Configure logging to emit warning and above to stderr
    and everything else to stdout. This behavior is provided
    for compatibility with distutils.log but may change in
    the future.
    """
    err_handler = logging.StreamHandler()
    err_handler.setLevel(logging.WARNING)
    out_handler = logging.StreamHandler(sys.stdout)
    out_handler.addFilter(_not_warning)
    handlers = err_handler, out_handler
    logging.basicConfig(
        format="{message}", style='{', handlers=handlers, level=logging.DEBUG
    )
    if inspect.ismodule(distutils.dist.log):
        monkey.patch_func(set_threshold, distutils.log, 'set_threshold')
        # For some reason `distutils.log` module is getting cached in `distutils.dist`
        # and then loaded again when patched,
        # implying: id(distutils.log) != id(distutils.dist.log).
        # Make sure the same module object is used everywhere:
        distutils.dist.log = distutils.log


def set_threshold(level: int) -> int:
    logging.root.setLevel(level * 10)
    return set_threshold.unpatched(level)



## File: modified.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/modified.py`*

```python

try:
    # Ensure a DistutilsError raised by these methods is the same as distutils.errors.DistutilsError
    from distutils._modified import (
        newer,
        newer_group,
        newer_pairwise,
        newer_pairwise_group,
    )
except ImportError:
    # fallback for SETUPTOOLS_USE_DISTUTILS=stdlib, because _modified never existed in stdlib
    from ._distutils._modified import (
        newer,
        newer_group,
        newer_pairwise,
        newer_pairwise_group,
    )

__all__ = ['newer', 'newer_pairwise', 'newer_group', 'newer_pairwise_group']



## File: monkey.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/monkey.py`*

```python

"""
Monkey patching of distutils.
"""

from __future__ import annotations

import inspect
import platform
import sys
import types
from typing import TypeVar, cast, overload

import distutils.filelist

_T = TypeVar("_T")
_UnpatchT = TypeVar("_UnpatchT", type, types.FunctionType)


__all__: list[str] = []
"""
Everything is private. Contact the project team
if you think you need this functionality.
"""


def _get_mro(cls):
    """
    Returns the bases classes for cls sorted by the MRO.

    Works around an issue on Jython where inspect.getmro will not return all
    base classes if multiple classes share the same name. Instead, this
    function will return a tuple containing the class itself, and the contents
    of cls.__bases__. See https://github.com/pypa/setuptools/issues/1024.
    """
    if platform.python_implementation() == "Jython":
        return (cls,) + cls.__bases__
    return inspect.getmro(cls)


@overload
def get_unpatched(item: _UnpatchT) -> _UnpatchT: ...
@overload
def get_unpatched(item: object) -> None: ...
def get_unpatched(
    item: type | types.FunctionType | object,
) -> type | types.FunctionType | None:
    if isinstance(item, type):
        return get_unpatched_class(item)
    if isinstance(item, types.FunctionType):
        return get_unpatched_function(item)
    return None


def get_unpatched_class(cls: type[_T]) -> type[_T]:
    """Protect against re-patching the distutils if reloaded

    Also ensures that no other distutils extension monkeypatched the distutils
    first.
    """
    external_bases = (
        cast(type[_T], cls)
        for cls in _get_mro(cls)
        if not cls.__module__.startswith('setuptools')
    )
    base = next(external_bases)
    if not base.__module__.startswith('distutils'):
        msg = f"distutils has already been patched by {cls!r}"
        raise AssertionError(msg)
    return base


def patch_all():
    import setuptools

    # we can't patch distutils.cmd, alas
    distutils.core.Command = setuptools.Command  # type: ignore[misc,assignment] # monkeypatching

    _patch_distribution_metadata()

    # Install Distribution throughout the distutils
    for module in distutils.dist, distutils.core, distutils.cmd:
        module.Distribution = setuptools.dist.Distribution

    # Install the patched Extension
    distutils.core.Extension = setuptools.extension.Extension  # type: ignore[misc,assignment] # monkeypatching
    distutils.extension.Extension = setuptools.extension.Extension  # type: ignore[misc,assignment] # monkeypatching
    if 'distutils.command.build_ext' in sys.modules:
        sys.modules[
            'distutils.command.build_ext'
        ].Extension = setuptools.extension.Extension


def _patch_distribution_metadata():
    from . import _core_metadata

    """Patch write_pkg_file and read_pkg_file for higher metadata standards"""
    for attr in (
        'write_pkg_info',
        'write_pkg_file',
        'read_pkg_file',
        'get_metadata_version',
        'get_fullname',
    ):
        new_val = getattr(_core_metadata, attr)
        setattr(distutils.dist.DistributionMetadata, attr, new_val)


def patch_func(replacement, target_mod, func_name):
    """
    Patch func_name in target_mod with replacement

    Important - original must be resolved by name to avoid
    patching an already patched function.
    """
    original = getattr(target_mod, func_name)

    # set the 'unpatched' attribute on the replacement to
    # point to the original.
    vars(replacement).setdefault('unpatched', original)

    # replace the function in the original module
    setattr(target_mod, func_name, replacement)


def get_unpatched_function(candidate):
    return candidate.unpatched



## File: msvc.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/msvc.py`*

```python

"""
Environment info about Microsoft Compilers.

>>> getfixture('windows_only')
>>> ei = EnvironmentInfo('amd64')
"""

from __future__ import annotations

import contextlib
import itertools
import json
import os
import os.path
import platform
from typing import TYPE_CHECKING, TypedDict

from more_itertools import unique_everseen

import distutils.errors

if TYPE_CHECKING:
    from typing_extensions import LiteralString, NotRequired

# https://github.com/python/mypy/issues/8166
if not TYPE_CHECKING and platform.system() == 'Windows':
    import winreg
    from os import environ
else:
    # Mock winreg and environ so the module can be imported on this platform.

    class winreg:
        HKEY_USERS = None
        HKEY_CURRENT_USER = None
        HKEY_LOCAL_MACHINE = None
        HKEY_CLASSES_ROOT = None

    environ: dict[str, str] = dict()


class PlatformInfo:
    """
    Current and Target Architectures information.

    Parameters
    ----------
    arch: str
        Target architecture.
    """

    current_cpu = environ.get('processor_architecture', '').lower()

    def __init__(self, arch) -> None:
        self.arch = arch.lower().replace('x64', 'amd64')

    @property
    def target_cpu(self):
        """
        Return Target CPU architecture.

        Return
        ------
        str
            Target CPU
        """
        return self.arch[self.arch.find('_') + 1 :]

    def target_is_x86(self):
        """
        Return True if target CPU is x86 32 bits..

        Return
        ------
        bool
            CPU is x86 32 bits
        """
        return self.target_cpu == 'x86'

    def current_is_x86(self):
        """
        Return True if current CPU is x86 32 bits..

        Return
        ------
        bool
            CPU is x86 32 bits
        """
        return self.current_cpu == 'x86'

    def current_dir(self, hidex86=False, x64=False) -> str:
        """
        Current platform specific subfolder.

        Parameters
        ----------
        hidex86: bool
            return '' and not '\x86' if architecture is x86.
        x64: bool
            return '\x64' and not '\amd64' if architecture is amd64.

        Return
        ------
        str
            subfolder: '\target', or '' (see hidex86 parameter)
        """
        return (
            ''
            if (self.current_cpu == 'x86' and hidex86)
            else r'\x64'
            if (self.current_cpu == 'amd64' and x64)
            else rf'\{self.current_cpu}'
        )

    def target_dir(self, hidex86=False, x64=False) -> str:
        r"""
        Target platform specific subfolder.

        Parameters
        ----------
        hidex86: bool
            return '' and not '\x86' if architecture is x86.
        x64: bool
            return '\x64' and not '\amd64' if architecture is amd64.

        Return
        ------
        str
            subfolder: '\current', or '' (see hidex86 parameter)
        """
        return (
            ''
            if (self.target_cpu == 'x86' and hidex86)
            else r'\x64'
            if (self.target_cpu == 'amd64' and x64)
            else rf'\{self.target_cpu}'
        )

    def cross_dir(self, forcex86=False):
        r"""
        Cross platform specific subfolder.

        Parameters
        ----------
        forcex86: bool
            Use 'x86' as current architecture even if current architecture is
            not x86.

        Return
        ------
        str
            subfolder: '' if target architecture is current architecture,
            '\current_target' if not.
        """
        current = 'x86' if forcex86 else self.current_cpu
        return (
            ''
            if self.target_cpu == current
            else self.target_dir().replace('\\', f'\\{current}_')
        )


class RegistryInfo:
    """
    Microsoft Visual Studio related registry information.

    Parameters
    ----------
    platform_info: PlatformInfo
        "PlatformInfo" instance.
    """

    HKEYS = (
        winreg.HKEY_USERS,
        winreg.HKEY_CURRENT_USER,
        winreg.HKEY_LOCAL_MACHINE,
        winreg.HKEY_CLASSES_ROOT,
    )

    def __init__(self, platform_info) -> None:
        self.pi = platform_info

    @property
    def visualstudio(self) -> str:
        """
        Microsoft Visual Studio root registry key.

        Return
        ------
        str
            Registry key
        """
        return 'VisualStudio'

    @property
    def sxs(self):
        """
        Microsoft Visual Studio SxS registry key.

        Return
        ------
        str
            Registry key
        """
        return os.path.join(self.visualstudio, 'SxS')

    @property
    def vc(self):
        """
        Microsoft Visual C++ VC7 registry key.

        Return
        ------
        str
            Registry key
        """
        return os.path.join(self.sxs, 'VC7')

    @property
    def vs(self):
        """
        Microsoft Visual Studio VS7 registry key.

        Return
        ------
        str
            Registry key
        """
        return os.path.join(self.sxs, 'VS7')

    @property
    def vc_for_python(self) -> str:
        """
        Microsoft Visual C++ for Python registry key.

        Return
        ------
        str
            Registry key
        """
        return r'DevDiv\VCForPython'

    @property
    def microsoft_sdk(self) -> str:
        """
        Microsoft SDK registry key.

        Return
        ------
        str
            Registry key
        """
        return 'Microsoft SDKs'

    @property
    def windows_sdk(self):
        """
        Microsoft Windows/Platform SDK registry key.

        Return
        ------
        str
            Registry key
        """
        return os.path.join(self.microsoft_sdk, 'Windows')

    @property
    def netfx_sdk(self):
        """
        Microsoft .NET Framework SDK registry key.

        Return
        ------
        str
            Registry key
        """
        return os.path.join(self.microsoft_sdk, 'NETFXSDK')

    @property
    def windows_kits_roots(self) -> str:
        """
        Microsoft Windows Kits Roots registry key.

        Return
        ------
        str
            Registry key
        """
        return r'Windows Kits\Installed Roots'

    def microsoft(self, key, x86=False):
        """
        Return key in Microsoft software registry.

        Parameters
        ----------
        key: str
            Registry key path where look.
        x86: str
            Force x86 software registry.

        Return
        ------
        str
            Registry key
        """
        node64 = '' if self.pi.current_is_x86() or x86 else 'Wow6432Node'
        return os.path.join('Software', node64, 'Microsoft', key)

    def lookup(self, key, name):
        """
        Look for values in registry in Microsoft software registry.

        Parameters
        ----------
        key: str
            Registry key path where look.
        name: str
            Value name to find.

        Return
        ------
        str
            value
        """
        key_read = winreg.KEY_READ
        openkey = winreg.OpenKey
        closekey = winreg.CloseKey
        ms = self.microsoft
        for hkey in self.HKEYS:
            bkey = None
            try:
                bkey = openkey(hkey, ms(key), 0, key_read)
            except OSError:
                if not self.pi.current_is_x86():
                    try:
                        bkey = openkey(hkey, ms(key, True), 0, key_read)
                    except OSError:
                        continue
                else:
                    continue
            try:
                return winreg.QueryValueEx(bkey, name)[0]
            except OSError:
                pass
            finally:
                if bkey:
                    closekey(bkey)
        return None


class SystemInfo:
    """
    Microsoft Windows and Visual Studio related system information.

    Parameters
    ----------
    registry_info: RegistryInfo
        "RegistryInfo" instance.
    vc_ver: float
        Required Microsoft Visual C++ version.
    """

    # Variables and properties in this class use originals CamelCase variables
    # names from Microsoft source files for more easy comparison.
    WinDir = environ.get('WinDir', '')
    ProgramFiles = environ.get('ProgramFiles', '')
    ProgramFilesx86 = environ.get('ProgramFiles(x86)', ProgramFiles)

    def __init__(self, registry_info, vc_ver=None) -> None:
        self.ri = registry_info
        self.pi = self.ri.pi

        self.known_vs_paths = self.find_programdata_vs_vers()

        # Except for VS15+, VC version is aligned with VS version
        self.vs_ver = self.vc_ver = vc_ver or self._find_latest_available_vs_ver()

    def _find_latest_available_vs_ver(self):
        """
        Find the latest VC version

        Return
        ------
        float
            version
        """
        reg_vc_vers = self.find_reg_vs_vers()

        if not (reg_vc_vers or self.known_vs_paths):
            raise distutils.errors.DistutilsPlatformError(
                'No Microsoft Visual C++ version found'
            )

        vc_vers = set(reg_vc_vers)
        vc_vers.update(self.known_vs_paths)
        return sorted(vc_vers)[-1]

    def find_reg_vs_vers(self):
        """
        Find Microsoft Visual Studio versions available in registry.

        Return
        ------
        list of float
            Versions
        """
        ms = self.ri.microsoft
        vckeys = (self.ri.vc, self.ri.vc_for_python, self.ri.vs)
        vs_vers = []
        for hkey, key in itertools.product(self.ri.HKEYS, vckeys):
            try:
                bkey = winreg.OpenKey(hkey, ms(key), 0, winreg.KEY_READ)
            except OSError:
                continue
            with bkey:
                subkeys, values, _ = winreg.QueryInfoKey(bkey)
                for i in range(values):
                    with contextlib.suppress(ValueError):
                        ver = float(winreg.EnumValue(bkey, i)[0])
                        if ver not in vs_vers:
                            vs_vers.append(ver)
                for i in range(subkeys):
                    with contextlib.suppress(ValueError):
                        ver = float(winreg.EnumKey(bkey, i))
                        if ver not in vs_vers:
                            vs_vers.append(ver)
        return sorted(vs_vers)

    def find_programdata_vs_vers(self) -> dict[float, str]:
        r"""
        Find Visual studio 2017+ versions from information in
        "C:\ProgramData\Microsoft\VisualStudio\Packages\_Instances".

        Return
        ------
        dict
            float version as key, path as value.
        """
        vs_versions: dict[float, str] = {}
        instances_dir = r'C:\ProgramData\Microsoft\VisualStudio\Packages\_Instances'

        try:
            hashed_names = os.listdir(instances_dir)

        except OSError:
            # Directory not exists with all Visual Studio versions
            return vs_versions

        for name in hashed_names:
            try:
                # Get VS installation path from "state.json" file
                state_path = os.path.join(instances_dir, name, 'state.json')
                with open(state_path, 'rt', encoding='utf-8') as state_file:
                    state = json.load(state_file)
                vs_path = state['installationPath']

                # Raises OSError if this VS installation does not contain VC
                os.listdir(os.path.join(vs_path, r'VC\Tools\MSVC'))

                # Store version and path
                vs_versions[self._as_float_version(state['installationVersion'])] = (
                    vs_path
                )

            except (OSError, KeyError):
                # Skip if "state.json" file is missing or bad format
                continue

        return vs_versions

    @staticmethod
    def _as_float_version(version):
        """
        Return a string version as a simplified float version (major.minor)

        Parameters
        ----------
        version: str
            Version.

        Return
        ------
        float
            version
        """
        return float('.'.join(version.split('.')[:2]))

    @property
    def VSInstallDir(self):
        """
        Microsoft Visual Studio directory.

        Return
        ------
        str
            path
        """
        # Default path
        default = os.path.join(
            self.ProgramFilesx86, f'Microsoft Visual Studio {self.vs_ver:0.1f}'
        )

        # Try to get path from registry, if fail use default path
        return self.ri.lookup(self.ri.vs, f'{self.vs_ver:0.1f}') or default

    @property
    def VCInstallDir(self):
        """
        Microsoft Visual C++ directory.

        Return
        ------
        str
            path
        """
        path = self._guess_vc() or self._guess_vc_legacy()

        if not os.path.isdir(path):
            msg = 'Microsoft Visual C++ directory not found'
            raise distutils.errors.DistutilsPlatformError(msg)

        return path

    def _guess_vc(self):
        """
        Locate Visual C++ for VS2017+.

        Return
        ------
        str
            path
        """
        if self.vs_ver <= 14.0:
            return ''

        try:
            # First search in known VS paths
            vs_dir = self.known_vs_paths[self.vs_ver]
        except KeyError:
            # Else, search with path from registry
            vs_dir = self.VSInstallDir

        guess_vc = os.path.join(vs_dir, r'VC\Tools\MSVC')

        # Subdir with VC exact version as name
        try:
            # Update the VC version with real one instead of VS version
            vc_ver = os.listdir(guess_vc)[-1]
            self.vc_ver = self._as_float_version(vc_ver)
            return os.path.join(guess_vc, vc_ver)
        except (OSError, IndexError):
            return ''

    def _guess_vc_legacy(self):
        """
        Locate Visual C++ for versions prior to 2017.

        Return
        ------
        str
            path
        """
        default = os.path.join(
            self.ProgramFilesx86,
            rf'Microsoft Visual Studio {self.vs_ver:0.1f}\VC',
        )

        # Try to get "VC++ for Python" path from registry as default path
        reg_path = os.path.join(self.ri.vc_for_python, f'{self.vs_ver:0.1f}')
        python_vc = self.ri.lookup(reg_path, 'installdir')
        default_vc = os.path.join(python_vc, 'VC') if python_vc else default

        # Try to get path from registry, if fail use default path
        return self.ri.lookup(self.ri.vc, f'{self.vs_ver:0.1f}') or default_vc

    @property
    def WindowsSdkVersion(self) -> tuple[LiteralString, ...]:
        """
        Microsoft Windows SDK versions for specified MSVC++ version.

        Return
        ------
        tuple of str
            versions
        """
        if self.vs_ver <= 9.0:
            return '7.0', '6.1', '6.0a'
        elif self.vs_ver == 10.0:
            return '7.1', '7.0a'
        elif self.vs_ver == 11.0:
            return '8.0', '8.0a'
        elif self.vs_ver == 12.0:
            return '8.1', '8.1a'
        elif self.vs_ver >= 14.0:
            return '10.0', '8.1'
        return ()

    @property
    def WindowsSdkLastVersion(self):
        """
        Microsoft Windows SDK last version.

        Return
        ------
        str
            version
        """
        return self._use_last_dir_name(os.path.join(self.WindowsSdkDir, 'lib'))

    @property
    def WindowsSdkDir(self) -> str | None:  # noqa: C901  # is too complex (12)  # FIXME
        """
        Microsoft Windows SDK directory.

        Return
        ------
        str
            path
        """
        sdkdir: str | None = ''
        for ver in self.WindowsSdkVersion:
            # Try to get it from registry
            loc = os.path.join(self.ri.windows_sdk, f'v{ver}')
            sdkdir = self.ri.lookup(loc, 'installationfolder')
            if sdkdir:
                break
        if not sdkdir or not os.path.isdir(sdkdir):
            # Try to get "VC++ for Python" version from registry
            path = os.path.join(self.ri.vc_for_python, f'{self.vc_ver:0.1f}')
            install_base = self.ri.lookup(path, 'installdir')
            if install_base:
                sdkdir = os.path.join(install_base, 'WinSDK')
        if not sdkdir or not os.path.isdir(sdkdir):
            # If fail, use default new path
            for ver in self.WindowsSdkVersion:
                intver = ver[: ver.rfind('.')]
                path = rf'Microsoft SDKs\Windows Kits\{intver}'
                d = os.path.join(self.ProgramFiles, path)
                if os.path.isdir(d):
                    sdkdir = d
        if not sdkdir or not os.path.isdir(sdkdir):
            # If fail, use default old path
            for ver in self.WindowsSdkVersion:
                path = rf'Microsoft SDKs\Windows\v{ver}'
                d = os.path.join(self.ProgramFiles, path)
                if os.path.isdir(d):
                    sdkdir = d
        if not sdkdir:
            # If fail, use Platform SDK
            sdkdir = os.path.join(self.VCInstallDir, 'PlatformSDK')
        return sdkdir

    @property
    def WindowsSDKExecutablePath(self):
        """
        Microsoft Windows SDK executable directory.

        Return
        ------
        str
            path
        """
        # Find WinSDK NetFx Tools registry dir name
        if self.vs_ver <= 11.0:
            netfxver = 35
            arch = ''
        else:
            netfxver = 40
            hidex86 = True if self.vs_ver <= 12.0 else False
            arch = self.pi.current_dir(x64=True, hidex86=hidex86).replace('\\', '-')
        fx = f'WinSDK-NetFx{netfxver}Tools{arch}'

        # list all possibles registry paths
        regpaths = []
        if self.vs_ver >= 14.0:
            for ver in self.NetFxSdkVersion:
                regpaths += [os.path.join(self.ri.netfx_sdk, ver, fx)]

        for ver in self.WindowsSdkVersion:
            regpaths += [os.path.join(self.ri.windows_sdk, f'v{ver}A', fx)]

        # Return installation folder from the more recent path
        for path in regpaths:
            execpath = self.ri.lookup(path, 'installationfolder')
            if execpath:
                return execpath

        return None

    @property
    def FSharpInstallDir(self):
        """
        Microsoft Visual F# directory.

        Return
        ------
        str
            path
        """
        path = os.path.join(self.ri.visualstudio, rf'{self.vs_ver:0.1f}\Setup\F#')
        return self.ri.lookup(path, 'productdir') or ''

    @property
    def UniversalCRTSdkDir(self):
        """
        Microsoft Universal CRT SDK directory.

        Return
        ------
        str
            path
        """
        # Set Kit Roots versions for specified MSVC++ version
        vers = ('10', '81') if self.vs_ver >= 14.0 else ()

        # Find path of the more recent Kit
        for ver in vers:
            sdkdir = self.ri.lookup(self.ri.windows_kits_roots, f'kitsroot{ver}')
            if sdkdir:
                return sdkdir or ''

        return None

    @property
    def UniversalCRTSdkLastVersion(self):
        """
        Microsoft Universal C Runtime SDK last version.

        Return
        ------
        str
            version
        """
        return self._use_last_dir_name(os.path.join(self.UniversalCRTSdkDir, 'lib'))

    @property
    def NetFxSdkVersion(self):
        """
        Microsoft .NET Framework SDK versions.

        Return
        ------
        tuple of str
            versions
        """
        # Set FxSdk versions for specified VS version
        return (
            ('4.7.2', '4.7.1', '4.7', '4.6.2', '4.6.1', '4.6', '4.5.2', '4.5.1', '4.5')
            if self.vs_ver >= 14.0
            else ()
        )

    @property
    def NetFxSdkDir(self):
        """
        Microsoft .NET Framework SDK directory.

        Return
        ------
        str
            path
        """
        sdkdir = ''
        for ver in self.NetFxSdkVersion:
            loc = os.path.join(self.ri.netfx_sdk, ver)
            sdkdir = self.ri.lookup(loc, 'kitsinstallationfolder')
            if sdkdir:
                break
        return sdkdir

    @property
    def FrameworkDir32(self):
        """
        Microsoft .NET Framework 32bit directory.

        Return
        ------
        str
            path
        """
        # Default path
        guess_fw = os.path.join(self.WinDir, r'Microsoft.NET\Framework')

        # Try to get path from registry, if fail use default path
        return self.ri.lookup(self.ri.vc, 'frameworkdir32') or guess_fw

    @property
    def FrameworkDir64(self):
        """
        Microsoft .NET Framework 64bit directory.

        Return
        ------
        str
            path
        """
        # Default path
        guess_fw = os.path.join(self.WinDir, r'Microsoft.NET\Framework64')

        # Try to get path from registry, if fail use default path
        return self.ri.lookup(self.ri.vc, 'frameworkdir64') or guess_fw

    @property
    def FrameworkVersion32(self) -> tuple[str, ...]:
        """
        Microsoft .NET Framework 32bit versions.

        Return
        ------
        tuple of str
            versions
        """
        return self._find_dot_net_versions(32)

    @property
    def FrameworkVersion64(self) -> tuple[str, ...]:
        """
        Microsoft .NET Framework 64bit versions.

        Return
        ------
        tuple of str
            versions
        """
        return self._find_dot_net_versions(64)

    def _find_dot_net_versions(self, bits) -> tuple[str, ...]:
        """
        Find Microsoft .NET Framework versions.

        Parameters
        ----------
        bits: int
            Platform number of bits: 32 or 64.

        Return
        ------
        tuple of str
            versions
        """
        # Find actual .NET version in registry
        reg_ver = self.ri.lookup(self.ri.vc, f'frameworkver{bits}')
        dot_net_dir = getattr(self, f'FrameworkDir{bits}')
        ver = reg_ver or self._use_last_dir_name(dot_net_dir, 'v') or ''

        # Set .NET versions for specified MSVC++ version
        if self.vs_ver >= 12.0:
            return ver, 'v4.0'
        elif self.vs_ver >= 10.0:
            return 'v4.0.30319' if ver.lower()[:2] != 'v4' else ver, 'v3.5'
        elif self.vs_ver == 9.0:
            return 'v3.5', 'v2.0.50727'
        elif self.vs_ver == 8.0:
            return 'v3.0', 'v2.0.50727'
        return ()

    @staticmethod
    def _use_last_dir_name(path, prefix=''):
        """
        Return name of the last dir in path or '' if no dir found.

        Parameters
        ----------
        path: str
            Use dirs in this path
        prefix: str
            Use only dirs starting by this prefix

        Return
        ------
        str
            name
        """
        matching_dirs = (
            dir_name
            for dir_name in reversed(os.listdir(path))
            if os.path.isdir(os.path.join(path, dir_name))
            and dir_name.startswith(prefix)
        )
        return next(matching_dirs, None) or ''


class _EnvironmentDict(TypedDict):
    include: str
    lib: str
    libpath: str
    path: str
    py_vcruntime_redist: NotRequired[str | None]


class EnvironmentInfo:
    """
    Return environment variables for specified Microsoft Visual C++ version
    and platform : Lib, Include, Path and libpath.

    This function is compatible with Microsoft Visual C++ 9.0 to 14.X.

    Script created by analysing Microsoft environment configuration files like
    "vcvars[...].bat", "SetEnv.Cmd", "vcbuildtools.bat", ...

    Parameters
    ----------
    arch: str
        Target architecture.
    vc_ver: float
        Required Microsoft Visual C++ version. If not set, autodetect the last
        version.
    vc_min_ver: float
        Minimum Microsoft Visual C++ version.
    """

    # Variables and properties in this class use originals CamelCase variables
    # names from Microsoft source files for more easy comparison.

    def __init__(self, arch, vc_ver=None, vc_min_ver=0) -> None:
        self.pi = PlatformInfo(arch)
        self.ri = RegistryInfo(self.pi)
        self.si = SystemInfo(self.ri, vc_ver)

        if self.vc_ver < vc_min_ver:
            err = 'No suitable Microsoft Visual C++ version found'
            raise distutils.errors.DistutilsPlatformError(err)

    @property
    def vs_ver(self):
        """
        Microsoft Visual Studio.

        Return
        ------
        float
            version
        """
        return self.si.vs_ver

    @property
    def vc_ver(self):
        """
        Microsoft Visual C++ version.

        Return
        ------
        float
            version
        """
        return self.si.vc_ver

    @property
    def VSTools(self):
        """
        Microsoft Visual Studio Tools.

        Return
        ------
        list of str
            paths
        """
        paths = [r'Common7\IDE', r'Common7\Tools']

        if self.vs_ver >= 14.0:
            arch_subdir = self.pi.current_dir(hidex86=True, x64=True)
            paths += [r'Common7\IDE\CommonExtensions\Microsoft\TestWindow']
            paths += [r'Team Tools\Performance Tools']
            paths += [rf'Team Tools\Performance Tools{arch_subdir}']

        return [os.path.join(self.si.VSInstallDir, path) for path in paths]

    @property
    def VCIncludes(self):
        """
        Microsoft Visual C++ & Microsoft Foundation Class Includes.

        Return
        ------
        list of str
            paths
        """
        return [
            os.path.join(self.si.VCInstallDir, 'Include'),
            os.path.join(self.si.VCInstallDir, r'ATLMFC\Include'),
        ]

    @property
    def VCLibraries(self):
        """
        Microsoft Visual C++ & Microsoft Foundation Class Libraries.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver >= 15.0:
            arch_subdir = self.pi.target_dir(x64=True)
        else:
            arch_subdir = self.pi.target_dir(hidex86=True)
        paths = [f'Lib{arch_subdir}', rf'ATLMFC\Lib{arch_subdir}']

        if self.vs_ver >= 14.0:
            paths += [rf'Lib\store{arch_subdir}']

        return [os.path.join(self.si.VCInstallDir, path) for path in paths]

    @property
    def VCStoreRefs(self):
        """
        Microsoft Visual C++ store references Libraries.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 14.0:
            return []
        return [os.path.join(self.si.VCInstallDir, r'Lib\store\references')]

    @property
    def VCTools(self):
        """
        Microsoft Visual C++ Tools.

        Return
        ------
        list of str
            paths

        When host CPU is ARM, the tools should be found for ARM.

        >>> getfixture('windows_only')
        >>> mp = getfixture('monkeypatch')
        >>> mp.setattr(PlatformInfo, 'current_cpu', 'arm64')
        >>> ei = EnvironmentInfo(arch='irrelevant')
        >>> paths = ei.VCTools
        >>> any('HostARM64' in path for path in paths)
        True
        """
        si = self.si
        tools = [os.path.join(si.VCInstallDir, 'VCPackages')]

        forcex86 = True if self.vs_ver <= 10.0 else False
        arch_subdir = self.pi.cross_dir(forcex86)
        if arch_subdir:
            tools += [os.path.join(si.VCInstallDir, f'Bin{arch_subdir}')]

        if self.vs_ver == 14.0:
            path = f'Bin{self.pi.current_dir(hidex86=True)}'
            tools += [os.path.join(si.VCInstallDir, path)]

        elif self.vs_ver >= 15.0:
            host_id = self.pi.current_cpu.replace('amd64', 'x64').upper()
            host_dir = os.path.join('bin', f'Host{host_id}%s')
            tools += [
                os.path.join(si.VCInstallDir, host_dir % self.pi.target_dir(x64=True))
            ]

            if self.pi.current_cpu != self.pi.target_cpu:
                tools += [
                    os.path.join(
                        si.VCInstallDir, host_dir % self.pi.current_dir(x64=True)
                    )
                ]

        else:
            tools += [os.path.join(si.VCInstallDir, 'Bin')]

        return tools

    @property
    def OSLibraries(self):
        """
        Microsoft Windows SDK Libraries.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver <= 10.0:
            arch_subdir = self.pi.target_dir(hidex86=True, x64=True)
            return [os.path.join(self.si.WindowsSdkDir, f'Lib{arch_subdir}')]

        else:
            arch_subdir = self.pi.target_dir(x64=True)
            lib = os.path.join(self.si.WindowsSdkDir, 'lib')
            libver = self._sdk_subdir
            return [os.path.join(lib, f'{libver}um{arch_subdir}')]

    @property
    def OSIncludes(self):
        """
        Microsoft Windows SDK Include.

        Return
        ------
        list of str
            paths
        """
        include = os.path.join(self.si.WindowsSdkDir, 'include')

        if self.vs_ver <= 10.0:
            return [include, os.path.join(include, 'gl')]

        else:
            if self.vs_ver >= 14.0:
                sdkver = self._sdk_subdir
            else:
                sdkver = ''
            return [
                os.path.join(include, f'{sdkver}shared'),
                os.path.join(include, f'{sdkver}um'),
                os.path.join(include, f'{sdkver}winrt'),
            ]

    @property
    def OSLibpath(self):
        """
        Microsoft Windows SDK Libraries Paths.

        Return
        ------
        list of str
            paths
        """
        ref = os.path.join(self.si.WindowsSdkDir, 'References')
        libpath = []

        if self.vs_ver <= 9.0:
            libpath += self.OSLibraries

        if self.vs_ver >= 11.0:
            libpath += [os.path.join(ref, r'CommonConfiguration\Neutral')]

        if self.vs_ver >= 14.0:
            libpath += [
                ref,
                os.path.join(self.si.WindowsSdkDir, 'UnionMetadata'),
                os.path.join(ref, 'Windows.Foundation.UniversalApiContract', '[IP_REDACTED]'),
                os.path.join(ref, 'Windows.Foundation.FoundationContract', '[IP_REDACTED]'),
                os.path.join(
                    ref, 'Windows.Networking.Connectivity.WwanContract', '[IP_REDACTED]'
                ),
                os.path.join(
                    self.si.WindowsSdkDir,
                    'ExtensionSDKs',
                    'Microsoft.VCLibs',
                    f'{self.vs_ver:0.1f}',
                    'References',
                    'CommonConfiguration',
                    'neutral',
                ),
            ]
        return libpath

    @property
    def SdkTools(self):
        """
        Microsoft Windows SDK Tools.

        Return
        ------
        list of str
            paths
        """
        return list(self._sdk_tools())

    def _sdk_tools(self):
        """
        Microsoft Windows SDK Tools paths generator.

        Return
        ------
        generator of str
            paths
        """
        if self.vs_ver < 15.0:
            bin_dir = 'Bin' if self.vs_ver <= 11.0 else r'Bin\x86'
            yield os.path.join(self.si.WindowsSdkDir, bin_dir)

        if not self.pi.current_is_x86():
            arch_subdir = self.pi.current_dir(x64=True)
            path = f'Bin{arch_subdir}'
            yield os.path.join(self.si.WindowsSdkDir, path)

        if self.vs_ver in (10.0, 11.0):
            if self.pi.target_is_x86():
                arch_subdir = ''
            else:
                arch_subdir = self.pi.current_dir(hidex86=True, x64=True)
            path = rf'Bin\NETFX 4.0 Tools{arch_subdir}'
            yield os.path.join(self.si.WindowsSdkDir, path)

        elif self.vs_ver >= 15.0:
            path = os.path.join(self.si.WindowsSdkDir, 'Bin')
            arch_subdir = self.pi.current_dir(x64=True)
            sdkver = self.si.WindowsSdkLastVersion
            yield os.path.join(path, f'{sdkver}{arch_subdir}')

        if self.si.WindowsSDKExecutablePath:
            yield self.si.WindowsSDKExecutablePath

    @property
    def _sdk_subdir(self):
        """
        Microsoft Windows SDK version subdir.

        Return
        ------
        str
            subdir
        """
        ucrtver = self.si.WindowsSdkLastVersion
        return (f'{ucrtver}\\') if ucrtver else ''

    @property
    def SdkSetup(self):
        """
        Microsoft Windows SDK Setup.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver > 9.0:
            return []

        return [os.path.join(self.si.WindowsSdkDir, 'Setup')]

    @property
    def FxTools(self):
        """
        Microsoft .NET Framework Tools.

        Return
        ------
        list of str
            paths
        """
        pi = self.pi
        si = self.si

        if self.vs_ver <= 10.0:
            include32 = True
            include64 = not pi.target_is_x86() and not pi.current_is_x86()
        else:
            include32 = pi.target_is_x86() or pi.current_is_x86()
            include64 = pi.current_cpu == 'amd64' or pi.target_cpu == 'amd64'

        tools = []
        if include32:
            tools += [
                os.path.join(si.FrameworkDir32, ver) for ver in si.FrameworkVersion32
            ]
        if include64:
            tools += [
                os.path.join(si.FrameworkDir64, ver) for ver in si.FrameworkVersion64
            ]
        return tools

    @property
    def NetFxSDKLibraries(self):
        """
        Microsoft .Net Framework SDK Libraries.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 14.0 or not self.si.NetFxSdkDir:
            return []

        arch_subdir = self.pi.target_dir(x64=True)
        return [os.path.join(self.si.NetFxSdkDir, rf'lib\um{arch_subdir}')]

    @property
    def NetFxSDKIncludes(self):
        """
        Microsoft .Net Framework SDK Includes.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 14.0 or not self.si.NetFxSdkDir:
            return []

        return [os.path.join(self.si.NetFxSdkDir, r'include\um')]

    @property
    def VsTDb(self):
        """
        Microsoft Visual Studio Team System Database.

        Return
        ------
        list of str
            paths
        """
        return [os.path.join(self.si.VSInstallDir, r'VSTSDB\Deploy')]

    @property
    def MSBuild(self):
        """
        Microsoft Build Engine.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 12.0:
            return []
        elif self.vs_ver < 15.0:
            base_path = self.si.ProgramFilesx86
            arch_subdir = self.pi.current_dir(hidex86=True)
        else:
            base_path = self.si.VSInstallDir
            arch_subdir = ''

        path = rf'MSBuild\{self.vs_ver:0.1f}\bin{arch_subdir}'
        build = [os.path.join(base_path, path)]

        if self.vs_ver >= 15.0:
            # Add Roslyn C# & Visual Basic Compiler
            build += [os.path.join(base_path, path, 'Roslyn')]

        return build

    @property
    def HTMLHelpWorkshop(self):
        """
        Microsoft HTML Help Workshop.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 11.0:
            return []

        return [os.path.join(self.si.ProgramFilesx86, 'HTML Help Workshop')]

    @property
    def UCRTLibraries(self):
        """
        Microsoft Universal C Runtime SDK Libraries.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 14.0:
            return []

        arch_subdir = self.pi.target_dir(x64=True)
        lib = os.path.join(self.si.UniversalCRTSdkDir, 'lib')
        ucrtver = self._ucrt_subdir
        return [os.path.join(lib, f'{ucrtver}ucrt{arch_subdir}')]

    @property
    def UCRTIncludes(self):
        """
        Microsoft Universal C Runtime SDK Include.

        Return
        ------
        list of str
            paths
        """
        if self.vs_ver < 14.0:
            return []

        include = os.path.join(self.si.UniversalCRTSdkDir, 'include')
        return [os.path.join(include, f'{self._ucrt_subdir}ucrt')]

    @property
    def _ucrt_subdir(self):
        """
        Microsoft Universal C Runtime SDK version subdir.

        Return
        ------
        str
            subdir
        """
        ucrtver = self.si.UniversalCRTSdkLastVersion
        return (f'{ucrtver}\\') if ucrtver else ''

    @property
    def FSharp(self):
        """
        Microsoft Visual F#.

        Return
        ------
        list of str
            paths
        """
        if 11.0 > self.vs_ver > 12.0:
            return []

        return [self.si.FSharpInstallDir]

    @property
    def VCRuntimeRedist(self) -> str | None:
        """
        Microsoft Visual C++ runtime redistributable dll.

        Returns the first suitable path found or None.
        """
        vcruntime = f'vcruntime{self.vc_ver}0.dll'
        arch_subdir = self.pi.target_dir(x64=True).strip('\\')

        # Installation prefixes candidates
        prefixes = []
        tools_path = self.si.VCInstallDir
        redist_path = os.path.dirname(tools_path.replace(r'\Tools', r'\Redist'))
        if os.path.isdir(redist_path):
            # Redist version may not be exactly the same as tools
            redist_path = os.path.join(redist_path, os.listdir(redist_path)[-1])
            prefixes += [redist_path, os.path.join(redist_path, 'onecore')]

        prefixes += [os.path.join(tools_path, 'redist')]  # VS14 legacy path

        # CRT directory
        crt_dirs = (
            f'Microsoft.VC{self.vc_ver * 10}.CRT',
            # Sometime store in directory with VS version instead of VC
            f'Microsoft.VC{int(self.vs_ver) * 10}.CRT',
        )

        # vcruntime path
        candidate_paths = (
            os.path.join(prefix, arch_subdir, crt_dir, vcruntime)
            for (prefix, crt_dir) in itertools.product(prefixes, crt_dirs)
        )
        return next(filter(os.path.isfile, candidate_paths), None)  # type: ignore[arg-type] #python/mypy#12682

    def return_env(self, exists: bool = True) -> _EnvironmentDict:
        """
        Return environment dict.

        Parameters
        ----------
        exists: bool
            It True, only return existing paths.

        Return
        ------
        dict
            environment
        """
        env = _EnvironmentDict(
            include=self._build_paths(
                'include',
                [
                    self.VCIncludes,
                    self.OSIncludes,
                    self.UCRTIncludes,
                    self.NetFxSDKIncludes,
                ],
                exists,
            ),
            lib=self._build_paths(
                'lib',
                [
                    self.VCLibraries,
                    self.OSLibraries,
                    self.FxTools,
                    self.UCRTLibraries,
                    self.NetFxSDKLibraries,
                ],
                exists,
            ),
            libpath=self._build_paths(
                'libpath',
                [self.VCLibraries, self.FxTools, self.VCStoreRefs, self.OSLibpath],
                exists,
            ),
            path=self._build_paths(
                'path',
                [
                    self.VCTools,
                    self.VSTools,
                    self.VsTDb,
                    self.SdkTools,
                    self.SdkSetup,
                    self.FxTools,
                    self.MSBuild,
                    self.HTMLHelpWorkshop,
                    self.FSharp,
                ],
                exists,
            ),
        )
        if self.vs_ver >= 14 and self.VCRuntimeRedist:
            env['py_vcruntime_redist'] = self.VCRuntimeRedist
        return env

    def _build_paths(self, name, spec_path_lists, exists):
        """
        Given an environment variable name and specified paths,
        return a pathsep-separated string of paths containing
        unique, extant, directories from those paths and from
        the environment variable. Raise an error if no paths
        are resolved.

        Parameters
        ----------
        name: str
            Environment variable name
        spec_path_lists: list of str
            Paths
        exists: bool
            It True, only return existing paths.

        Return
        ------
        str
            Pathsep-separated paths
        """
        # flatten spec_path_lists
        spec_paths = itertools.chain.from_iterable(spec_path_lists)
        env_paths = environ.get(name, '').split(os.pathsep)
        paths = itertools.chain(spec_paths, env_paths)
        extant_paths = list(filter(os.path.isdir, paths)) if exists else paths
        if not extant_paths:
            msg = f"{name.upper()} environment variable is empty"
            raise distutils.errors.DistutilsPlatformError(msg)
        unique_paths = unique_everseen(extant_paths)
        return os.pathsep.join(unique_paths)



## File: namespaces.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/namespaces.py`*

```python

import itertools
import os

from .compat import py312

from distutils import log

flatten = itertools.chain.from_iterable


class Installer:
    nspkg_ext = '-nspkg.pth'

    def install_namespaces(self) -> None:
        nsp = self._get_all_ns_packages()
        if not nsp:
            return
        filename = self._get_nspkg_file()
        self.outputs.append(filename)
        log.info("Installing %s", filename)
        lines = map(self._gen_nspkg_line, nsp)

        if self.dry_run:
            # always generate the lines, even in dry run
            list(lines)
            return

        with open(filename, 'wt', encoding=py312.PTH_ENCODING) as f:
            # Python<3.13 requires encoding="locale" instead of "utf-8"
            # See: python/cpython#77102
            f.writelines(lines)

    def uninstall_namespaces(self) -> None:
        filename = self._get_nspkg_file()
        if not os.path.exists(filename):
            return
        log.info("Removing %s", filename)
        os.remove(filename)

    def _get_nspkg_file(self):
        filename, _ = os.path.splitext(self._get_target())
        return filename + self.nspkg_ext

    def _get_target(self):
        return self.target

    _nspkg_tmpl = (
        "import sys, types, os",
        "p = os.path.join(%(root)s, *%(pth)r)",
        "importlib = __import__('importlib.util')",
        "__import__('importlib.machinery')",
        (
            "m = "
            "sys.modules.setdefault(%(pkg)r, "
            "importlib.util.module_from_spec("
            "importlib.machinery.PathFinder.find_spec(%(pkg)r, "
            "[os.path.dirname(p)])))"
        ),
        ("m = m or sys.modules.setdefault(%(pkg)r, types.ModuleType(%(pkg)r))"),
        "mp = (m or []) and m.__dict__.setdefault('__path__',[])",
        "(p not in mp) and mp.append(p)",
    )
    "lines for the namespace installer"

    _nspkg_tmpl_multi = ('m and setattr(sys.modules[%(parent)r], %(child)r, m)',)
    "additional line(s) when a parent package is indicated"

    def _get_root(self):
        return "sys._getframe(1).f_locals['sitedir']"

    def _gen_nspkg_line(self, pkg):
        pth = tuple(pkg.split('.'))
        root = self._get_root()
        tmpl_lines = self._nspkg_tmpl
        parent, sep, child = pkg.rpartition('.')
        if parent:
            tmpl_lines += self._nspkg_tmpl_multi
        return ';'.join(tmpl_lines) % locals() + '\n'

    def _get_all_ns_packages(self):
        """Return sorted list of all package namespaces"""
        pkgs = self.distribution.namespace_packages or []
        return sorted(set(flatten(map(self._pkg_names, pkgs))))

    @staticmethod
    def _pkg_names(pkg):
        """
        Given a namespace package, yield the components of that
        package.

        >>> names = Installer._pkg_names('a.b.c')
        >>> set(names) == set(['a', 'a.b', 'a.b.c'])
        True
        """
        parts = pkg.split('.')
        while parts:
            yield '.'.join(parts)
            parts.pop()


class DevelopInstaller(Installer):
    def _get_root(self):
        return repr(str(self.egg_path))

    def _get_target(self):
        return self.egg_link



## File: unicode_utils.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/unicode_utils.py`*

```python

import sys
import unicodedata
from configparser import RawConfigParser

from .compat import py39
from .warnings import SetuptoolsDeprecationWarning


# HFS Plus uses decomposed UTF-8
def decompose(path):
    if isinstance(path, str):
        return unicodedata.normalize('NFD', path)
    try:
        path = path.decode('utf-8')
        path = unicodedata.normalize('NFD', path)
        path = path.encode('utf-8')
    except UnicodeError:
        pass  # Not UTF-8
    return path


def filesys_decode(path):
    """
    Ensure that the given path is decoded,
    ``None`` when no expected encoding works
    """

    if isinstance(path, str):
        return path

    fs_enc = sys.getfilesystemencoding() or 'utf-8'
    candidates = fs_enc, 'utf-8'

    for enc in candidates:
        try:
            return path.decode(enc)
        except UnicodeDecodeError:
            continue

    return None


def try_encode(string, enc):
    "turn unicode encoding into a functional routine"
    try:
        return string.encode(enc)
    except UnicodeEncodeError:
        return None


def _read_utf8_with_fallback(file: str, fallback_encoding=py39.LOCALE_ENCODING) -> str:
    """
    First try to read the file with UTF-8, if there is an error fallback to a
    different encoding ("locale" by default). Returns the content of the file.
    Also useful when reading files that might have been produced by an older version of
    setuptools.
    """
    try:
        with open(file, "r", encoding="utf-8") as f:
            return f.read()
    except UnicodeDecodeError:  # pragma: no cover
        _Utf8EncodingNeeded.emit(file=file, fallback_encoding=fallback_encoding)
        with open(file, "r", encoding=fallback_encoding) as f:
            return f.read()


def _cfg_read_utf8_with_fallback(
    cfg: RawConfigParser, file: str, fallback_encoding=py39.LOCALE_ENCODING
) -> None:
    """Same idea as :func:`_read_utf8_with_fallback`, but for the
    :meth:`RawConfigParser.read` method.

    This method may call ``cfg.clear()``.
    """
    try:
        cfg.read(file, encoding="utf-8")
    except UnicodeDecodeError:  # pragma: no cover
        _Utf8EncodingNeeded.emit(file=file, fallback_encoding=fallback_encoding)
        cfg.clear()
        cfg.read(file, encoding=fallback_encoding)


class _Utf8EncodingNeeded(SetuptoolsDeprecationWarning):
    _SUMMARY = """
    `encoding="utf-8"` fails with {file!r}, trying `encoding={fallback_encoding!r}`.
    """

    _DETAILS = """
    Fallback behavior for UTF-8 is considered **deprecated** and future versions of
    `setuptools` may not implement it.

    Please encode {file!r} with "utf-8" to ensure future builds will succeed.

    If this file was produced by `setuptools` itself, cleaning up the cached files
    and re-building/re-installing the package with a newer version of `setuptools`
    (e.g. by updating `build-system.requires` in its `pyproject.toml`)
    might solve the problem.
    """
    # TODO: Add a deadline?
    #       Will we be able to remove this?
    #       The question comes to mind mainly because of sdists that have been produced
    #       by old versions of setuptools and published to PyPI...



## File: version.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/version.py`*

```python

from ._importlib import metadata

try:
    __version__ = metadata.version('setuptools') or '0.dev0+unknown'
except Exception:
    __version__ = '0.dev0+unknown'



## File: warnings.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/warnings.py`*

```python

"""Provide basic warnings used by setuptools modules.

Using custom classes (other than ``UserWarning``) allow users to set
``PYTHONWARNINGS`` filters to run tests and prepare for upcoming changes in
setuptools.
"""

from __future__ import annotations

import os
import warnings
from datetime import date
from inspect import cleandoc
from textwrap import indent
from typing import TYPE_CHECKING

if TYPE_CHECKING:
    from typing_extensions import TypeAlias

_DueDate: TypeAlias = tuple[int, int, int]  # time tuple
_INDENT = 8 * " "
_TEMPLATE = f"""{80 * '*'}\n{{details}}\n{80 * '*'}"""


class SetuptoolsWarning(UserWarning):
    """Base class in ``setuptools`` warning hierarchy."""

    @classmethod
    def emit(
        cls,
        summary: str | None = None,
        details: str | None = None,
        due_date: _DueDate | None = None,
        see_docs: str | None = None,
        see_url: str | None = None,
        stacklevel: int = 2,
        **kwargs,
    ) -> None:
        """Private: reserved for ``setuptools`` internal use only"""
        # Default values:
        summary_ = summary or getattr(cls, "_SUMMARY", None) or ""
        details_ = details or getattr(cls, "_DETAILS", None) or ""
        due_date = due_date or getattr(cls, "_DUE_DATE", None)
        docs_ref = see_docs or getattr(cls, "_SEE_DOCS", None)
        docs_url = docs_ref and f"https://setuptools.pypa.io/en/latest/{docs_ref}"
        see_url = see_url or getattr(cls, "_SEE_URL", None)
        due = date(*due_date) if due_date else None

        text = cls._format(summary_, details_, due, see_url or docs_url, kwargs)
        if due and due < date.today() and _should_enforce():
            raise cls(text)
        warnings.warn(text, cls, stacklevel=stacklevel + 1)

    @classmethod
    def _format(
        cls,
        summary: str,
        details: str,
        due_date: date | None = None,
        see_url: str | None = None,
        format_args: dict | None = None,
    ) -> str:
        """Private: reserved for ``setuptools`` internal use only"""
        today = date.today()
        summary = cleandoc(summary).format_map(format_args or {})
        possible_parts = [
            cleandoc(details).format_map(format_args or {}),
            (
                f"\nBy {due_date:%Y-%b-%d}, you need to update your project and remove "
                "deprecated calls\nor your builds will no longer be supported."
                if due_date and due_date > today
                else None
            ),
            (
                "\nThis deprecation is overdue, please update your project and remove "
                "deprecated\ncalls to avoid build errors in the future."
                if due_date and due_date < today
                else None
            ),
            (f"\nSee {see_url} for details." if see_url else None),
        ]
        parts = [x for x in possible_parts if x]
        if parts:
            body = indent(_TEMPLATE.format(details="\n".join(parts)), _INDENT)
            return "\n".join([summary, "!!\n", body, "\n!!"])
        return summary


class InformationOnly(SetuptoolsWarning):
    """Currently there is no clear way of displaying messages to the users
    that use the setuptools backend directly via ``pip``.
    The only thing that might work is a warning, although it is not the
    most appropriate tool for the job...

    See pypa/packaging-problems#558.
    """


class SetuptoolsDeprecationWarning(SetuptoolsWarning):
    """
    Base class for warning deprecations in ``setuptools``

    This class is not derived from ``DeprecationWarning``, and as such is
    visible by default.
    """


def _should_enforce():
    enforce = os.getenv("SETUPTOOLS_ENFORCE_DEPRECATION", "false").lower()
    return enforce in ("true", "on", "ok", "1")



## File: wheel.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/wheel.py`*

```python

"""Wheels support."""

import contextlib
import email
import functools
import itertools
import os
import posixpath
import re
import zipfile

from packaging.requirements import Requirement
from packaging.tags import sys_tags
from packaging.utils import canonicalize_name
from packaging.version import Version as parse_version

import setuptools
from setuptools.archive_util import _unpack_zipfile_obj
from setuptools.command.egg_info import _egg_basename, write_requirements

from ._discovery import extras_from_deps
from ._importlib import metadata
from .unicode_utils import _read_utf8_with_fallback

from distutils.util import get_platform

WHEEL_NAME = re.compile(
    r"""^(?P<project_name>.+?)-(?P<version>\d.*?)
    ((-(?P<build>\d.*?))?-(?P<py_version>.+?)-(?P<abi>.+?)-(?P<platform>.+?)
    )\.whl$""",
    re.VERBOSE,
).match

NAMESPACE_PACKAGE_INIT = "__import__('pkg_resources').declare_namespace(__name__)\n"


@functools.cache
def _get_supported_tags():
    # We calculate the supported tags only once, otherwise calling
    # this method on thousands of wheels takes seconds instead of
    # milliseconds.
    return {(t.interpreter, t.abi, t.platform) for t in sys_tags()}


def unpack(src_dir, dst_dir) -> None:
    """Move everything under `src_dir` to `dst_dir`, and delete the former."""
    for dirpath, dirnames, filenames in os.walk(src_dir):
        subdir = os.path.relpath(dirpath, src_dir)
        for f in filenames:
            src = os.path.join(dirpath, f)
            dst = os.path.join(dst_dir, subdir, f)
            os.renames(src, dst)
        for n, d in reversed(list(enumerate(dirnames))):
            src = os.path.join(dirpath, d)
            dst = os.path.join(dst_dir, subdir, d)
            if not os.path.exists(dst):
                # Directory does not exist in destination,
                # rename it and prune it from os.walk list.
                os.renames(src, dst)
                del dirnames[n]
    # Cleanup.
    for dirpath, dirnames, filenames in os.walk(src_dir, topdown=True):
        assert not filenames
        os.rmdir(dirpath)


@contextlib.contextmanager
def disable_info_traces():
    """
    Temporarily disable info traces.
    """
    from distutils import log

    saved = log.set_threshold(log.WARN)
    try:
        yield
    finally:
        log.set_threshold(saved)


class Wheel:
    def __init__(self, filename) -> None:
        match = WHEEL_NAME(os.path.basename(filename))
        if match is None:
            raise ValueError(f'invalid wheel name: {filename!r}')
        self.filename = filename
        for k, v in match.groupdict().items():
            setattr(self, k, v)

    def tags(self):
        """List tags (py_version, abi, platform) supported by this wheel."""
        return itertools.product(
            self.py_version.split('.'),
            self.abi.split('.'),
            self.platform.split('.'),
        )

    def is_compatible(self):
        """Is the wheel compatible with the current platform?"""
        return next((True for t in self.tags() if t in _get_supported_tags()), False)

    def egg_name(self):
        return (
            _egg_basename(
                self.project_name,
                self.version,
                platform=(None if self.platform == 'any' else get_platform()),
            )
            + ".egg"
        )

    def get_dist_info(self, zf):
        # find the correct name of the .dist-info dir in the wheel file
        for member in zf.namelist():
            dirname = posixpath.dirname(member)
            if dirname.endswith('.dist-info') and canonicalize_name(dirname).startswith(
                canonicalize_name(self.project_name)
            ):
                return dirname
        raise ValueError("unsupported wheel format. .dist-info not found")

    def install_as_egg(self, destination_eggdir) -> None:
        """Install wheel as an egg directory."""
        with zipfile.ZipFile(self.filename) as zf:
            self._install_as_egg(destination_eggdir, zf)

    def _install_as_egg(self, destination_eggdir, zf):
        dist_basename = f'{self.project_name}-{self.version}'
        dist_info = self.get_dist_info(zf)
        dist_data = f'{dist_basename}.data'
        egg_info = os.path.join(destination_eggdir, 'EGG-INFO')

        self._convert_metadata(zf, destination_eggdir, dist_info, egg_info)
        self._move_data_entries(destination_eggdir, dist_data)
        self._fix_namespace_packages(egg_info, destination_eggdir)

    @staticmethod
    def _convert_metadata(zf, destination_eggdir, dist_info, egg_info):
        def get_metadata(name):
            with zf.open(posixpath.join(dist_info, name)) as fp:
                value = fp.read().decode('utf-8')
                return email.parser.Parser().parsestr(value)

        wheel_metadata = get_metadata('WHEEL')
        # Check wheel format version is supported.
        wheel_version = parse_version(wheel_metadata.get('Wheel-Version'))
        wheel_v1 = parse_version('1.0') <= wheel_version < parse_version('2.0dev0')
        if not wheel_v1:
            raise ValueError(f'unsupported wheel format version: {wheel_version}')
        # Extract to target directory.
        _unpack_zipfile_obj(zf, destination_eggdir)
        dist_info = os.path.join(destination_eggdir, dist_info)
        install_requires, extras_require = Wheel._convert_requires(
            destination_eggdir, dist_info
        )
        os.rename(dist_info, egg_info)
        os.rename(
            os.path.join(egg_info, 'METADATA'),
            os.path.join(egg_info, 'PKG-INFO'),
        )
        setup_dist = setuptools.Distribution(
            attrs=dict(
                install_requires=install_requires,
                extras_require=extras_require,
            ),
        )
        with disable_info_traces():
            write_requirements(
                setup_dist.get_command_obj('egg_info'),
                None,
                os.path.join(egg_info, 'requires.txt'),
            )

    @staticmethod
    def _convert_requires(destination_eggdir, dist_info):
        md = metadata.Distribution.at(dist_info).metadata
        deps = md.get_all('Requires-Dist') or []
        reqs = list(map(Requirement, deps))

        extras = extras_from_deps(deps)

        # Note: Evaluate and strip markers now,
        # as it's difficult to convert back from the syntax:
        # foobar; "linux" in sys_platform and extra == 'test'
        def raw_req(req):
            req = Requirement(str(req))
            req.marker = None
            return str(req)

        def eval(req, **env):
            return not req.marker or req.marker.evaluate(env)

        def for_extra(req):
            try:
                markers = req.marker._markers
            except AttributeError:
                markers = ()
            return set(
                marker[2].value
                for marker in markers
                if isinstance(marker, tuple) and marker[0].value == 'extra'
            )

        install_requires = list(
            map(raw_req, filter(eval, itertools.filterfalse(for_extra, reqs)))
        )
        extras_require = {
            extra: list(
                map(
                    raw_req,
                    (req for req in reqs if for_extra(req) and eval(req, extra=extra)),
                )
            )
            for extra in extras
        }
        return install_requires, extras_require

    @staticmethod
    def _move_data_entries(destination_eggdir, dist_data):
        """Move data entries to their correct location."""
        dist_data = os.path.join(destination_eggdir, dist_data)
        dist_data_scripts = os.path.join(dist_data, 'scripts')
        if os.path.exists(dist_data_scripts):
            egg_info_scripts = os.path.join(destination_eggdir, 'EGG-INFO', 'scripts')
            os.mkdir(egg_info_scripts)
            for entry in os.listdir(dist_data_scripts):
                # Remove bytecode, as it's not properly handled
                # during easy_install scripts install phase.
                if entry.endswith('.pyc'):
                    os.unlink(os.path.join(dist_data_scripts, entry))
                else:
                    os.rename(
                        os.path.join(dist_data_scripts, entry),
                        os.path.join(egg_info_scripts, entry),
                    )
            os.rmdir(dist_data_scripts)
        for subdir in filter(
            os.path.exists,
            (
                os.path.join(dist_data, d)
                for d in ('data', 'headers', 'purelib', 'platlib')
            ),
        ):
            unpack(subdir, destination_eggdir)
        if os.path.exists(dist_data):
            os.rmdir(dist_data)

    @staticmethod
    def _fix_namespace_packages(egg_info, destination_eggdir):
        namespace_packages = os.path.join(egg_info, 'namespace_packages.txt')
        if os.path.exists(namespace_packages):
            namespace_packages = _read_utf8_with_fallback(namespace_packages).split()

            for mod in namespace_packages:
                mod_dir = os.path.join(destination_eggdir, *mod.split('.'))
                mod_init = os.path.join(mod_dir, '__init__.py')
                if not os.path.exists(mod_dir):
                    os.mkdir(mod_dir)
                if not os.path.exists(mod_init):
                    with open(mod_init, 'w', encoding="utf-8") as fp:
                        fp.write(NAMESPACE_PACKAGE_INIT)



## File: windows_support.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/windows_support.py`*

```python

import platform


def windows_only(func):
    if platform.system() != 'Windows':
        return lambda *args, **kwargs: None
    return func


@windows_only
def hide_file(path: str) -> None:
    """
    Set the hidden attribute on a file or directory.

    From https://stackoverflow.com/questions/19622133/

    `path` must be text.
    """
    import ctypes
    import ctypes.wintypes

    SetFileAttributes = ctypes.windll.kernel32.SetFileAttributesW
    SetFileAttributes.argtypes = ctypes.wintypes.LPWSTR, ctypes.wintypes.DWORD
    SetFileAttributes.restype = ctypes.wintypes.BOOL

    FILE_ATTRIBUTE_HIDDEN = 0x02

    ret = SetFileAttributes(path, FILE_ATTRIBUTE_HIDDEN)
    if not ret:
        raise ctypes.WinError()



## File: __init__.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/_distutils/__init__.py`*

```python

import importlib
import sys

__version__, _, _ = sys.version.partition(' ')


try:
    # Allow Debian and pkgsrc (only) to customize system
    # behavior. Ref pypa/distutils#2 and pypa/distutils#16.
    # This hook is deprecated and no other environments
    # should use it.
    importlib.import_module('_distutils_system_mod')
except ImportError:
    pass



## File: _log.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/_distutils/_log.py`*

```python

import logging

log = logging.getLogger()



## File: _macos_compat.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/_distutils/_macos_compat.py`*

```python

import importlib
import sys


def bypass_compiler_fixup(cmd, args):
    return cmd


if sys.platform == 'darwin':
    compiler_fixup = importlib.import_module('_osx_support').compiler_fixup
else:
    compiler_fixup = bypass_compiler_fixup



## File: _modified.py
*Path: `/data/data/com.termux/files/home/downloads/social-analyzer/venv/lib/python3.12/site-packages/setuptools/_distutils/_modified.py`*

```python

"""Timestamp comparison of files and groups of files."""

from __future__ import annotations

import functools
import os.path
from collections.abc import Callable, Iterable
from typing import Literal, TypeVar

from jaraco.functools import splat

from .compat.py39 import zip_strict
from .errors import DistutilsFileError

_SourcesT = TypeVar(
    "_SourcesT", bound="str | bytes | os.PathLike[str] | os.PathLike[bytes]"
)
_TargetsT = TypeVar(
    "_TargetsT", bound="str | bytes | os.PathLike[str] | os.PathLike[bytes]"
)


def _newer(source, target):
    return not os.path.exists(target) or (
        os.path.getmtime(source) > os.path.getmtime(target)
    )


def newer(
    source: str | bytes | os.PathLike[str] | os.PathLike[bytes],
    target: str | bytes | os.PathLike[str] | os.PathLike[bytes],
) -> bool:
    """
    Is source modified more recently than target.

    Returns True if 'source' is modified more recently than
    'target' or if 'target' does not exist.

    Raises DistutilsFileError if 'source' does not exist.
    """
    if not os.path.exists(source):
        raise DistutilsFileError(f"file {os.path.abspath(source)!r} does not exist")

    return _newer(source, target)


def newer_pairwise(
    sources: Iterable[_SourcesT],
    targets: Iterable[_TargetsT],
    newer: Callable[[_SourcesT, _TargetsT], bool] = newer,
) -> tuple[list[_SourcesT], list[_TargetsT]]:
    """
    Filter filenames where sources are newer than targets.

    Walk two filename iterables in parallel, testing if each source is newer
    than its corresponding target.  Returns a pair of lists (sources,
    targets) where source is newer than target, according to the semantics
    of 'newer()'.
    """
    newer_pairs = filter(splat(newer), zip_strict(sources, targets))
    return tuple(map(list, zip(*newer_pairs))) or ([], [])


def newer_group(
    sources: Iterable[str | bytes | os.PathLike[str] | os.PathLike[bytes]],
    target: str | bytes | os.PathLike[str] | os.PathLike[bytes],
    missing: Literal["error", "ignore", "newer"] = "error",
) -> bool:
    """
    Is target out-of-date with respect to any file in sources.

    Return True if 'target' is out-of-date with respect to any file
    listed in 'sources'. In other words, if 'target' exists and is newer
    than every file in 'sources', return False; otherwise return True.
    ``missing`` controls how to handle a missing source file:

    - error (default): allow the ``stat()`` call to fail.
    - ignore: silently disregard any missing source files.
    - newer: treat missing source files as "target out of date". This
      mode is handy in "dry-run" mode: it will pretend to carry out
      commands that wouldn't work because inputs are missing, but
      that doesn't matter because dry-run won't run the commands.
    """

    def missing_as_newer(source):
        return missing == 'newer' and not os.path.exists(source)

    ignored = os.path.exists if missing == 'ignore' else None
    return not os.path.exists(target) or any(
        missing_as_newer(source) or _newer(source, target)
        for source in filter(ignored, sources)
    )


newer_pairwise_group = functools.partial(newer_pairwise, newer=newer_group)



