```typescript
import * as fs from "fs";
import {
  Document,
  Packer,
  Paragraph,
  TextRun,
  HeadingLevel,
  AlignmentType,
} from "docx";

// Sample Resume Data (You'd fetch or define this)
const resumeData = {
  name: "Ada Lovelace",
  title: "Software Engineer",
  contact: {
    email: "ada@example.com",
    phone: "123-456-7890",
    linkedin: "linkedin.com/in/ada",
  },
  summary:
    "Experienced Software Engineer specializing in analytical engines and early computing concepts.",
  experience: [
    {
      title: "Lead Analyst",
      company: "Babbage Analytical Engine Project",
      years: "1842-1843",
      details: [
        "Developed the first algorithm intended to be carried out by a machine.",
        "Recognized the potential for computers beyond mere calculation.",
      ],
    },
  ],
  // ... other sections like skills, education
};

// Create the document
const doc = new Document({
  creator: "Your Resume App",
  title: `${resumeData.name} - Resume`,
  description: `Resume for ${resumeData.name}`,
  sections: [
    {
      properties: {}, // Page setup, etc.
      children: [
        // Name (Centered, Large, Bold)
        new Paragraph({
          children: [
            new TextRun({
              text: resumeData.name,
              bold: true,
              size: 40, // Half-points, so 20pt font
              font: "Calibri", // Example font
            }),
          ],
          alignment: AlignmentType.CENTER,
        }),
        // Title (Centered, smaller)
        new Paragraph({
          children: [
            new TextRun({
              text: resumeData.title,
              size: 28, // 14pt
              font: "Calibri",
            }),
          ],
          alignment: AlignmentType.CENTER,
          spacing: { after: 200 }, // Spacing after paragraph (Twips)
        }),

        // --- Contact Info Section (Example) ---
        // You'd likely format this better, maybe in a table or columns

        new Paragraph({
          children: [
            new TextRun({ text: `Email: ${resumeData.contact.email}` }),
          ],
          // Add more styling like indentation, spacing if needed
        }),
        new Paragraph({
          children: [
            new TextRun({ text: `LinkedIn: ${resumeData.contact.linkedin}` }),
          ],
        }),

        // --- Summary Section ---
        new Paragraph({
          children: [new TextRun("Summary")],
          heading: HeadingLevel.HEADING_1, // Use built-in heading styles
          spacing: { before: 400, after: 100 },
        }),
        new Paragraph({
          children: [
            new TextRun({
              text: resumeData.summary,
              size: 22,
              font: "Calibri",
            }),
          ],
        }),

        // --- Experience Section ---
        new Paragraph({
          children: [new TextRun("Experience")],
          heading: HeadingLevel.HEADING_1,
          spacing: { before: 400, after: 100 },
        }),
        // Loop through experience
        ...resumeData.experience.flatMap((job) => [
          new Paragraph({
            children: [
              new TextRun({ text: job.title, bold: true, size: 24 }),
              new TextRun({ text: ` | ${job.company}`, size: 24 }),
              new TextRun({ text: ` (${job.years})`, italics: true, size: 24 }),
            ],
            spacing: { after: 50 },
          }),
          // Job details (bullet points)
          ...job.details.map(
            (detail) =>
              new Paragraph({
                children: [new TextRun({ text: detail, size: 22 })],
                bullet: { level: 0 }, // Basic bullet point
                indent: { left: 720 }, // Indentation (Twips)
                spacing: { after: 50 },
              })
          ),
        ]),

        // ... Add other sections similarly (Skills, Education, etc.)
      ],
    },
  ],
});

// Generate the buffer
Packer.toBuffer(doc)
  .then((buffer) => {
    fs.writeFileSync(
      `${resumeData.name.replace(" ", "_")}_Resume.docx`,
      buffer
    );
    console.log("Resume generated!");
  })
  .catch((err) => {
    console.error("Error generating document:", err);
  });
```
