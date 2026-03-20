from bs4 import BeautifulSoup
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet

# read saved page
with open("page.html", "r", encoding="utf-8", errors="ignore") as f:
    soup = BeautifulSoup(f.read(), "html.parser")

# plain text extraction
text = soup.get_text(strip=True)

doc = SimpleDocTemplate("THREAD.pdf", pagesize=letter)
styles = getSampleStyleSheet()
story = [Paragraph("FULL THREAD", styles["Title"]), Spacer(1, 12)]

for line in text.splitlines():
    if line.strip():
        story.append(Paragraph(line.strip(), styles["Normal"]))
        story.append(Spacer(1, 6))

doc.build(story)
print("✅ THREAD.pdf created successfully")
