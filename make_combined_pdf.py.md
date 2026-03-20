import csv
from reportlab.lib.pagesizes import letter
from reportlab.platypus import SimpleDocTemplate, Table, TableStyle, Paragraph, Spacer
from reportlab.lib.styles import getSampleStyleSheet
from reportlab.lib import colors
from pypdf import PdfWriter, PdfReader

def csv_to_pdf(csv_filename, pdf_filename):
    doc = SimpleDocTemplate(pdf_filename, pagesize=letter)
    styles = getSampleStyleSheet()
    data = []
    
    with open(csv_filename, 'r', encoding='utf-8') as f:
        reader = csv.reader(f)
        for row in reader:
            data.append(row)
    
    table = Table(data)
    table.setStyle(TableStyle([
        ('BACKGROUND', (0, 0), (-1, 0), colors.grey),
        ('TEXTCOLOR', (0, 0), (-1, 0), colors.whitesmoke),
        ('ALIGN', (0, 0), (-1, -1), 'CENTER'),
        ('FONTNAME', (0, 0), (-1, 0), 'Helvetica-Bold'),
        ('FONTSIZE', (0, 0), (-1, 0), 12),
        ('BOTTOMPADDING', (0, 0), (-1, 0), 12),
        ('BACKGROUND', (0, 1), (-1, -1), colors.beige),
        ('GRID', (0, 0), (-1, -1), 1, colors.black)
    ]))
    
    story = [Paragraph(f"Table: {csv_filename}", styles['Title']), Spacer(1, 12), table]
    doc.build(story)

# Convert CSVs to PDFs
csv1 = "Tactic-ExamplefromMessagesVoicemails-IntentionalityEvidence-ImpactonJanelle.csv"
csv2 = "CycleStage-HisPattern-HerResponse-Outcome.csv"
pdf1 = "Tactic-ExamplefromMessagesVoicemails-IntentionalityEvidence-ImpactonJanelle.pdf"
pdf2 = "CycleStage-HisPattern-HerResponse-Outcome.pdf"

csv_to_pdf(csv1, pdf1)
csv_to_pdf(csv2, pdf2)

# Now merge all three PDFs
writer = PdfWriter()
files = [
    "i want you to assist me in a hypothetical behavioral social study via the(1).pdf",
    pdf1,
    pdf2
]

for name in files:
    reader = PdfReader(name, "rb")
    for page in reader.pages:
        writer.add_page(page)

with open("Combined-Study-Pack.pdf", "wb") as output:
    writer.write(output)

print("✅ Combined-Study-Pack.pdf created!")
