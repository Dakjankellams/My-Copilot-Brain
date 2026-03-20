
import React, { useState } from 'react';
import { DeviceData, SearchHistoryItem } from '../types';
import { ResourceCard } from './ResourceCard';
import { AuditVault } from './AuditVault';

interface Props {
  data: DeviceData;
  history: SearchHistoryItem[];
  onExport: (data: DeviceData) => void;
  onSelectAudit: (data: DeviceData) => void;
}

export const DeviceDashboard: React.FC<Props> = ({ data, history, onExport, onSelectAudit }) => {
  const [copiedScript, setCopiedScript] = useState<string | null>(null);

  const copyToClipboard = (text: string, label: string) => {
    navigator.clipboard.writeText(text);
    setCopiedScript(label);
    setTimeout(() => setCopiedScript(null), 2000);
  };

  const criticalHazards = data.insights.hazardsAndRecalls.filter(h => h.severity === 'CRITICAL');
  const otherHazards = data.insights.hazardsAndRecalls.filter(h => h.severity !== 'CRITICAL');

  return (
    <div className="animate-in fade-in slide-in-from-bottom-4 duration-700 space-y-12 pb-32">
      
      {/* 1. CRITICAL RECALL OVERRIDE */}
      {criticalHazards.length > 0 && (
        <section className="relative group">
          <div className="absolute -inset-1 bg-gradient-to-r from-rose-600 to-orange-600 rounded-[40px] blur opacity-50 group-hover:opacity-100 transition duration-1000"></div>
          <div className="relative bg-[#250a0a] border-2 border-rose-500/30 rounded-[38px] p-8 md:p-12 text-white overflow-hidden">
             <div className="absolute top-0 right-0 p-12 opacity-5 text-8xl font-black italic tracking-tighter select-none pointer-events-none">DANGER</div>
             <div className="flex flex-col md:flex-row items-center md:items-start justify-between gap-8 mb-10 relative z-10">
                <div className="flex items-center space-x-6">
                  <span className="text-7xl">🚩</span>
                  <div>
                    <h2 className="text-4xl font-black uppercase tracking-tighter leading-none">Safety Recall Alert</h2>
                    <p className="text-rose-400 font-black uppercase tracking-widest text-[10px] mt-2 flex items-center">
                       <span className="w-2 h-2 bg-rose-500 rounded-full mr-2 animate-ping"></span>
                       Immediate verification required for {data.model}
                    </p>
                  </div>
                </div>
                <div className="bg-rose-500/20 backdrop-blur-xl px-6 py-3 rounded-xl border border-rose-500/30">
                   <p className="text-[9px] font-black uppercase tracking-widest text-rose-300">Severity Level</p>
                   <p className="text-xl font-black text-white">LEVEL 10 CRITICAL</p>
                </div>
             </div>
             
             <div className="grid grid-cols-1 md:grid-cols-2 gap-6 relative z-10">
                {criticalHazards.map((h, i) => (
                  <div key={i} className="bg-black/40 backdrop-blur-md p-8 rounded-[28px] border border-rose-500/10 hover:border-rose-500/30 transition-all">
                    <h3 className="text-xl font-black mb-3 text-rose-100">{h.title}</h3>
                    <p className="text-sm font-medium text-slate-300 leading-relaxed mb-6">{h.description}</p>
                    <div className="bg-white p-5 rounded-2xl text-rose-950 shadow-xl">
                      <p className="text-[8px] font-black uppercase tracking-[0.2em] text-rose-600 mb-1">Response Protocol</p>
                      <p className="text-sm font-black leading-tight">{h.actionRequired}</p>
                    </div>
                  </div>
                ))}
             </div>
          </div>
        </section>
      )}

      {/* 2. DEVICE HERO */}
      <div className="bg-[#14171f] border border-white/5 rounded-[40px] p-8 md:p-12 shadow-2xl relative overflow-hidden group">
        <div className="absolute top-0 right-0 w-[400px] h-[400px] bg-indigo-600/5 blur-[100px] rounded-full group-hover:bg-indigo-600/10 transition-all duration-1000"></div>
        <div className="flex flex-col xl:flex-row justify-between items-start gap-10 relative z-10">
          <div className="space-y-6">
            <div className="flex items-center space-x-3">
               <span className="px-3 py-1 bg-indigo-600 text-white rounded-md text-[9px] font-black uppercase tracking-widest">Master Intel</span>
               <span className="text-slate-600">|</span>
               <span className="text-slate-400 text-[9px] font-black uppercase tracking-widest">{data.manufacturer} Division</span>
            </div>
            <h1 className="text-6xl font-black text-white tracking-tighter leading-none">{data.model}</h1>
            <div className="flex items-center space-x-6">
              <p className="text-slate-400 font-medium text-lg italic border-l-2 border-indigo-500 pl-4">Optimization for <span className="text-indigo-400 font-black">{data.os}</span>.</p>
              <div className="flex space-x-2">
                 <span className="px-3 py-1 bg-emerald-500/10 text-emerald-400 rounded-full text-[9px] font-bold border border-emerald-500/20 uppercase tracking-widest">Grounding 1:1</span>
                 <span className="px-3 py-1 bg-indigo-500/10 text-indigo-400 rounded-full text-[9px] font-bold border border-indigo-500/20 uppercase tracking-widest">Bios Vetted</span>
              </div>
            </div>
          </div>
          <div className="flex flex-wrap gap-4 pt-4 xl:pt-0">
            <button 
              onClick={() => onExport(data)} 
              className="bg-white hover:bg-slate-100 text-[#0a0c10] px-8 py-4 rounded-xl text-[11px] font-black uppercase shadow-2xl transition-all active:scale-95 flex items-center"
            >
              <span className="mr-3">📂</span> Download Engineering Report
            </button>
          </div>
        </div>
      </div>

      {/* 3. SYSTEM ENTRY & HARDWARE PROFILE */}
      <div className="grid grid-cols-1 lg:grid-cols-3 gap-8">
        {/* Entry Protocols */}
        <section className="lg:col-span-2 space-y-6">
          <h2 className="text-lg font-black text-white flex items-center uppercase tracking-widest">
             <span className="w-10 h-10 rounded-xl bg-indigo-600/20 text-indigo-400 flex items-center justify-center mr-4 text-xl">⌨️</span>
             System Entry Sequences
          </h2>
          <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
            {data.insights.systemEntryProtocols.map((protocol, i) => (
              <div key={i} className="bg-[#14171f] p-6 rounded-3xl border border-white/5 hover:border-indigo-500/30 transition-all">
                <div className="flex justify-between items-center mb-4">
                  <span className="text-indigo-400 text-[9px] font-black uppercase tracking-widest">{protocol.mode}</span>
                  <span className={`px-2 py-0.5 rounded-full text-[8px] font-black uppercase tracking-widest ${protocol.reliability === 'High' ? 'bg-emerald-500/20 text-emerald-400' : 'bg-amber-500/20 text-amber-400'}`}>
                    {protocol.reliability}
                  </span>
                </div>
                <code className="text-2xl font-black text-white block mb-2 font-mono tracking-tighter">
                  {protocol.sequence}
                </code>
                <p className="text-[11px] text-slate-500 font-medium">{protocol.description}</p>
              </div>
            ))}
          </div>
        </section>

        {/* Hardware Specs */}
        <section className="space-y-6">
          <h2 className="text-lg font-black text-white flex items-center uppercase tracking-widest">
             <span className="w-10 h-10 rounded-xl bg-slate-800 text-slate-400 flex items-center justify-center mr-4 text-xl">📊</span>
             Component Specs
          </h2>
          <div className="bg-[#14171f] p-8 rounded-[32px] border border-white/5 space-y-5">
            {Object.entries(data.specs).map(([key, val]) => (
              <div key={key} className="flex justify-between items-start border-b border-white/5 pb-3 last:border-0 last:pb-0">
                <span className="text-[10px] font-black text-slate-500 uppercase tracking-widest">{key}</span>
                <span className="text-[11px] font-bold text-slate-200 text-right max-w-[150px]">{val || 'Detect Failure'}</span>
              </div>
            ))}
          </div>
        </section>
      </div>

      {/* 4. TRI-VECTOR AUTOMATION SUITE (EXPANDED PER REQUEST) */}
      <section className="bg-[#0f1117] rounded-[48px] p-10 border border-white/5 shadow-inner">
        <div className="flex flex-col md:flex-row justify-between items-end mb-10 gap-6">
          <div className="space-y-2">
            <h2 className="text-4xl font-black tracking-tighter text-white">Forensic Automation Suite</h2>
            <p className="text-slate-500 font-bold text-xs uppercase tracking-widest">Triple-Vector Cross-Platform Scripting Matrix</p>
          </div>
          {copiedScript && (
            <div className="px-4 py-2 bg-emerald-500 text-white rounded-lg text-[10px] font-black uppercase tracking-widest animate-in fade-in slide-in-from-right-2">
              Copied {copiedScript} Sequence
            </div>
          )}
        </div>

        <div className="grid grid-cols-1 xl:grid-cols-3 gap-6">
          {/* WINDOWS CMD */}
          <div className="bg-black/60 border border-white/5 rounded-3xl overflow-hidden flex flex-col">
            <div className="bg-[#1a1c23] px-6 py-4 flex justify-between items-center border-b border-white/5">
               <div className="flex items-center space-x-3">
                  <span className="w-2 h-2 rounded-full bg-blue-500"></span>
                  <span className="text-[10px] font-black text-white uppercase tracking-widest">Admin CMD (Windows)</span>
               </div>
               <button 
                onClick={() => copyToClipboard(data.insights.automationScripts.cmd, 'CMD')}
                className="text-[9px] font-black text-indigo-400 hover:text-white transition-colors"
               >
                 COPY SCRIPT
               </button>
            </div>
            <pre className="p-6 text-xs text-blue-300 font-mono overflow-x-auto whitespace-pre-wrap flex-grow bg-black/20">
              <code>{data.insights.automationScripts.cmd}</code>
            </pre>
          </div>

          {/* WSL / LINUX */}
          <div className="bg-black/60 border border-white/5 rounded-3xl overflow-hidden flex flex-col">
            <div className="bg-[#1a1c23] px-6 py-4 flex justify-between items-center border-b border-white/5">
               <div className="flex items-center space-x-3">
                  <span className="w-2 h-2 rounded-full bg-orange-500"></span>
                  <span className="text-[10px] font-black text-white uppercase tracking-widest">WSL / Ubuntu (Linux)</span>
               </div>
               <button 
                onClick={() => copyToClipboard(data.insights.automationScripts.wsl, 'WSL')}
                className="text-[9px] font-black text-indigo-400 hover:text-white transition-colors"
               >
                 COPY SCRIPT
               </button>
            </div>
            <pre className="p-6 text-xs text-orange-300 font-mono overflow-x-auto whitespace-pre-wrap flex-grow bg-black/20">
              <code>{data.insights.automationScripts.wsl}</code>
            </pre>
          </div>

          {/* TERMUX */}
          <div className="bg-black/60 border border-white/5 rounded-3xl overflow-hidden flex flex-col">
            <div className="bg-[#1a1c23] px-6 py-4 flex justify-between items-center border-b border-white/5">
               <div className="flex items-center space-x-3">
                  <span className="w-2 h-2 rounded-full bg-emerald-500"></span>
                  <span className="text-[10px] font-black text-white uppercase tracking-widest">Termux (Android)</span>
               </div>
               <button 
                onClick={() => copyToClipboard(data.insights.automationScripts.termux, 'Termux')}
                className="text-[9px] font-black text-indigo-400 hover:text-white transition-colors"
               >
                 COPY SCRIPT
               </button>
            </div>
            <pre className="p-6 text-xs text-emerald-300 font-mono overflow-x-auto whitespace-pre-wrap flex-grow bg-black/20">
              <code>{data.insights.automationScripts.termux}</code>
            </pre>
          </div>
        </div>
      </section>

      {/* 5. INTELLIGENCE GROUNDING */}
      <section className="space-y-6">
        <h2 className="text-xs font-black text-slate-500 uppercase tracking-[0.3em] flex items-center">
          Verified Evidence Log
          <span className="ml-4 h-[1px] flex-grow bg-white/5"></span>
        </h2>
        <div className="flex flex-wrap gap-3">
          {data.groundingSources?.map((s, i) => (
            <a 
              key={i} 
              href={s.url} 
              target="_blank" 
              className="px-5 py-3 bg-[#14171f] border border-white/5 rounded-xl text-[10px] font-bold text-slate-400 hover:text-indigo-400 hover:border-indigo-500/50 hover:bg-indigo-500/5 transition-all flex items-center shadow-lg"
            >
              <span className="mr-3 opacity-30 text-xs">🔗</span>
              {s.title}
            </a>
          ))}
        </div>
      </section>

      <AuditVault history={history} onSelect={onSelectAudit} onExport={onExport} />
    </div>
  );
};
