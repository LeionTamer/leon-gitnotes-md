```typescript
"use client";

import { useState } from "react";
import { zodResolver } from "@hookform/resolvers/zod";
import { useForm } from "react-hook-form";
import { z } from "zod";
import { PlusCircle, Trash2 } from "lucide-react";

import { Button } from "@/components/ui/button";
import {
  Card,
  CardContent,
  CardDescription,
  CardFooter,
  CardHeader,
  CardTitle,
} from "@/components/ui/card";
import {
  Form,
  FormControl,
  FormDescription,
  FormField,
  FormItem,
  FormLabel,
  FormMessage,
} from "@/components/ui/form";
import { Input } from "@/components/ui/input";
import { Textarea } from "@/components/ui/textarea";
import { Separator } from "@/components/ui/separator";

const personalInfoSchema = z.object({
  name: z.string().min(2, { message: "Name must be at least 2 characters." }),
  email: z.string().email({ message: "Please enter a valid email address." }),
  phone: z.string().min(10, { message: "Please enter a valid phone number." }),
  location: z
    .string()
    .min(2, { message: "Location must be at least 2 characters." }),
  linkedin: z.string().optional(),
  github: z.string().optional(),
  portfolio: z.string().optional(),
});

const workExperienceSchema = z.object({
  jobTitle: z
    .string()
    .min(2, { message: "Job title must be at least 2 characters." }),
  company: z
    .string()
    .min(2, { message: "Company name must be at least 2 characters." }),
  startDate: z.string().min(1, { message: "Start date is required." }),
  endDate: z.string().optional(),
  current: z.boolean().optional(),
  responsibilities: z
    .string()
    .min(10, { message: "Please provide job responsibilities." }),
});

const educationSchema = z.object({
  degree: z
    .string()
    .min(2, { message: "Degree must be at least 2 characters." }),
  university: z
    .string()
    .min(2, { message: "University name must be at least 2 characters." }),
  startDate: z.string().min(1, { message: "Start date is required." }),
  endDate: z.string().optional(),
  current: z.boolean().optional(),
  description: z.string().optional(),
});

const projectSchema = z.object({
  name: z
    .string()
    .min(2, { message: "Project name must be at least 2 characters." }),
  description: z
    .string()
    .min(10, { message: "Please provide a project description." }),
  technologies: z
    .string()
    .min(2, { message: "Please list technologies used." }),
  link: z.string().optional(),
});

const formSchema = z.object({
  personalInfo: personalInfoSchema,
  workExperience: z
    .array(workExperienceSchema)
    .min(1, { message: "Add at least one work experience." }),
  education: z
    .array(educationSchema)
    .min(1, { message: "Add at least one education entry." }),
  skills: z.string().min(5, { message: "Please list your skills." }),
  projects: z.array(projectSchema).optional(),
});

export default function ResumeForm() {
  const form = useForm<z.infer<typeof formSchema>>({
    resolver: zodResolver(formSchema),
    defaultValues: {
      personalInfo: {
        name: "",
        email: "",
        phone: "",
        location: "",
        linkedin: "",
        github: "",
        portfolio: "",
      },
      workExperience: [
        {
          jobTitle: "",
          company: "",
          startDate: "",
          endDate: "",
          responsibilities: "",
        },
      ],
      education: [
        {
          degree: "",
          university: "",
          startDate: "",
          endDate: "",
          description: "",
        },
      ],
      skills: "",
      projects: [
        {
          name: "",
          description: "",
          technologies: "",
          link: "",
        },
      ],
    },
  });

  const [resumeData, setResumeData] = useState<z.infer<
    typeof formSchema
  > | null>(null);

  function onSubmit(values: z.infer<typeof formSchema>) {
    setResumeData(values);
    console.log(values);
    // Here you would typically send the data to an API or generate a resume
    alert("Resume data submitted successfully! Check the console for details.");
  }

  return (
    <div className="space-y-10 mb-10">
      <Form {...form}>
        <form onSubmit={form.handleSubmit(onSubmit)} className="space-y-8">
          {/* Personal Information */}
          <Card>
            <CardHeader>
              <CardTitle>Personal Information</CardTitle>
              <CardDescription>
                Enter your basic contact information.
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                <FormField
                  control={form.control}
                  name="personalInfo.name"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Full Name</FormLabel>
                      <FormControl>
                        <Input placeholder="John Doe" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
                <FormField
                  control={form.control}
                  name="personalInfo.email"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Email</FormLabel>
                      <FormControl>
                        <Input placeholder="john.doe@example.com" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
              </div>
              <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                <FormField
                  control={form.control}
                  name="personalInfo.phone"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Phone</FormLabel>
                      <FormControl>
                        <Input placeholder="(123) 456-7890" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
                <FormField
                  control={form.control}
                  name="personalInfo.location"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Location</FormLabel>
                      <FormControl>
                        <Input placeholder="City, State" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
              </div>
              <div className="grid grid-cols-1 md:grid-cols-3 gap-4">
                <FormField
                  control={form.control}
                  name="personalInfo.linkedin"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>LinkedIn (optional)</FormLabel>
                      <FormControl>
                        <Input
                          placeholder="linkedin.com/in/johndoe"
                          {...field}
                        />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
                <FormField
                  control={form.control}
                  name="personalInfo.github"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>GitHub (optional)</FormLabel>
                      <FormControl>
                        <Input placeholder="github.com/johndoe" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
                <FormField
                  control={form.control}
                  name="personalInfo.portfolio"
                  render={({ field }) => (
                    <FormItem>
                      <FormLabel>Portfolio (optional)</FormLabel>
                      <FormControl>
                        <Input placeholder="johndoe.dev" {...field} />
                      </FormControl>
                      <FormMessage />
                    </FormItem>
                  )}
                />
              </div>
            </CardContent>
          </Card>

          {/* Work Experience */}
          <Card>
            <CardHeader>
              <CardTitle>Work Experience</CardTitle>
              <CardDescription>
                Add your relevant work experience.
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              {form.watch("workExperience").map((_, index) => (
                <div key={index} className="space-y-4">
                  {index > 0 && <Separator className="my-4" />}
                  <div className="flex justify-between items-center">
                    <h3 className="text-lg font-medium">
                      Position {index + 1}
                    </h3>
                    {index > 0 && (
                      <Button
                        type="button"
                        variant="destructive"
                        size="sm"
                        onClick={() => {
                          const currentExperiences =
                            form.getValues("workExperience");
                          form.setValue(
                            "workExperience",
                            currentExperiences.filter((_, i) => i !== index)
                          );
                        }}
                      >
                        <Trash2 className="h-4 w-4 mr-1" />
                        Remove
                      </Button>
                    )}
                  </div>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <FormField
                      control={form.control}
                      name={`workExperience.${index}.jobTitle`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Job Title</FormLabel>
                          <FormControl>
                            <Input placeholder="Software Engineer" {...field} />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                    <FormField
                      control={form.control}
                      name={`workExperience.${index}.company`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Company</FormLabel>
                          <FormControl>
                            <Input placeholder="Acme Inc." {...field} />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                  </div>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <FormField
                      control={form.control}
                      name={`workExperience.${index}.startDate`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Start Date</FormLabel>
                          <FormControl>
                            <Input placeholder="MM/YYYY" {...field} />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                    <FormField
                      control={form.control}
                      name={`workExperience.${index}.endDate`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>End Date (or "Present")</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="MM/YYYY or Present"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                  </div>
                  <FormField
                    control={form.control}
                    name={`workExperience.${index}.responsibilities`}
                    render={({ field }) => (
                      <FormItem>
                        <FormLabel>Responsibilities & Achievements</FormLabel>
                        <FormControl>
                          <Textarea
                            placeholder="Describe your key responsibilities and achievements..."
                            className="min-h-[100px]"
                            {...field}
                          />
                        </FormControl>
                        <FormDescription>
                          Use bullet points for better readability (e.g., "•
                          Developed...")
                        </FormDescription>
                        <FormMessage />
                      </FormItem>
                    )}
                  />
                </div>
              ))}
              <Button
                type="button"
                variant="outline"
                className="mt-2"
                onClick={() => {
                  const currentExperiences = form.getValues("workExperience");
                  form.setValue("workExperience", [
                    ...currentExperiences,
                    {
                      jobTitle: "",
                      company: "",
                      startDate: "",
                      endDate: "",
                      responsibilities: "",
                    },
                  ]);
                }}
              >
                <PlusCircle className="h-4 w-4 mr-2" />
                Add Another Position
              </Button>
            </CardContent>
          </Card>

          {/* Education */}
          <Card>
            <CardHeader>
              <CardTitle>Education</CardTitle>
              <CardDescription>
                Add your educational background.
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              {form.watch("education").map((_, index) => (
                <div key={index} className="space-y-4">
                  {index > 0 && <Separator className="my-4" />}
                  <div className="flex justify-between items-center">
                    <h3 className="text-lg font-medium">
                      Education {index + 1}
                    </h3>
                    {index > 0 && (
                      <Button
                        type="button"
                        variant="destructive"
                        size="sm"
                        onClick={() => {
                          const currentEducation = form.getValues("education");
                          form.setValue(
                            "education",
                            currentEducation.filter((_, i) => i !== index)
                          );
                        }}
                      >
                        <Trash2 className="h-4 w-4 mr-1" />
                        Remove
                      </Button>
                    )}
                  </div>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <FormField
                      control={form.control}
                      name={`education.${index}.degree`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Degree</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="B.S. Computer Science"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                    <FormField
                      control={form.control}
                      name={`education.${index}.university`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>University/Institution</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="University of Technology"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                  </div>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <FormField
                      control={form.control}
                      name={`education.${index}.startDate`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Start Date</FormLabel>
                          <FormControl>
                            <Input placeholder="MM/YYYY" {...field} />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                    <FormField
                      control={form.control}
                      name={`education.${index}.endDate`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>End Date (or "Present")</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="MM/YYYY or Present"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                  </div>
                  <FormField
                    control={form.control}
                    name={`education.${index}.description`}
                    render={({ field }) => (
                      <FormItem>
                        <FormLabel>Additional Information (optional)</FormLabel>
                        <FormControl>
                          <Textarea
                            placeholder="Relevant coursework, honors, activities..."
                            className="min-h-[80px]"
                            {...field}
                          />
                        </FormControl>
                        <FormMessage />
                      </FormItem>
                    )}
                  />
                </div>
              ))}
              <Button
                type="button"
                variant="outline"
                className="mt-2"
                onClick={() => {
                  const currentEducation = form.getValues("education");
                  form.setValue("education", [
                    ...currentEducation,
                    {
                      degree: "",
                      university: "",
                      startDate: "",
                      endDate: "",
                      description: "",
                    },
                  ]);
                }}
              >
                <PlusCircle className="h-4 w-4 mr-2" />
                Add Another Education
              </Button>
            </CardContent>
          </Card>

          {/* Skills */}
          <Card>
            <CardHeader>
              <CardTitle>Skills</CardTitle>
              <CardDescription>
                List your technical skills and technologies.
              </CardDescription>
            </CardHeader>
            <CardContent>
              <FormField
                control={form.control}
                name="skills"
                render={({ field }) => (
                  <FormItem>
                    <FormControl>
                      <Textarea
                        placeholder="JavaScript, React, Node.js, Python, AWS, Docker, Git, Agile..."
                        className="min-h-[100px]"
                        {...field}
                      />
                    </FormControl>
                    <FormDescription>
                      Separate skills with commas. Group by categories if
                      needed.
                    </FormDescription>
                    <FormMessage />
                  </FormItem>
                )}
              />
            </CardContent>
          </Card>

          {/* Projects */}
          <Card>
            <CardHeader>
              <CardTitle>Projects</CardTitle>
              <CardDescription>
                Showcase your relevant projects.
              </CardDescription>
            </CardHeader>
            <CardContent className="space-y-4">
              {form.watch("projects")?.map((_, index) => (
                <div key={index} className="space-y-4">
                  {index > 0 && <Separator className="my-4" />}
                  <div className="flex justify-between items-center">
                    <h3 className="text-lg font-medium">Project {index + 1}</h3>
                    {index > 0 && (
                      <Button
                        type="button"
                        variant="destructive"
                        size="sm"
                        onClick={() => {
                          const currentProjects =
                            form.getValues("projects") || [];
                          form.setValue(
                            "projects",
                            currentProjects.filter((_, i) => i !== index)
                          );
                        }}
                      >
                        <Trash2 className="h-4 w-4 mr-1" />
                        Remove
                      </Button>
                    )}
                  </div>
                  <div className="grid grid-cols-1 md:grid-cols-2 gap-4">
                    <FormField
                      control={form.control}
                      name={`projects.${index}.name`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Project Name</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="E-commerce Platform"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                    <FormField
                      control={form.control}
                      name={`projects.${index}.link`}
                      render={({ field }) => (
                        <FormItem>
                          <FormLabel>Project Link (optional)</FormLabel>
                          <FormControl>
                            <Input
                              placeholder="https://github.com/username/project"
                              {...field}
                            />
                          </FormControl>
                          <FormMessage />
                        </FormItem>
                      )}
                    />
                  </div>
                  <FormField
                    control={form.control}
                    name={`projects.${index}.description`}
                    render={({ field }) => (
                      <FormItem>
                        <FormLabel>Description</FormLabel>
                        <FormControl>
                          <Textarea
                            placeholder="Describe the project, your role, and key features..."
                            className="min-h-[80px]"
                            {...field}
                          />
                        </FormControl>
                        <FormMessage />
                      </FormItem>
                    )}
                  />
                  <FormField
                    control={form.control}
                    name={`projects.${index}.technologies`}
                    render={({ field }) => (
                      <FormItem>
                        <FormLabel>Technologies Used</FormLabel>
                        <FormControl>
                          <Input
                            placeholder="React, Node.js, MongoDB, AWS"
                            {...field}
                          />
                        </FormControl>
                        <FormMessage />
                      </FormItem>
                    )}
                  />
                </div>
              ))}
              <Button
                type="button"
                variant="outline"
                className="mt-2"
                onClick={() => {
                  const currentProjects = form.getValues("projects") || [];
                  form.setValue("projects", [
                    ...currentProjects,
                    {
                      name: "",
                      description: "",
                      technologies: "",
                      link: "",
                    },
                  ]);
                }}
              >
                <PlusCircle className="h-4 w-4 mr-2" />
                Add Another Project
              </Button>
            </CardContent>
          </Card>

          <div className="flex justify-end">
            <Button type="submit" size="lg">
              Generate Resume
            </Button>
          </div>
        </form>
      </Form>

      {resumeData && (
        <Card className="mt-8">
          <CardHeader>
            <CardTitle>Resume Preview</CardTitle>
            <CardDescription>
              Here's a preview of your resume data. You can copy this or export
              it.
            </CardDescription>
          </CardHeader>
          <CardContent>
            <pre className="bg-muted p-4 rounded-md overflow-auto max-h-[500px]">
              {JSON.stringify(resumeData, null, 2)}
            </pre>
          </CardContent>
          <CardFooter>
            <Button
              onClick={() => {
                navigator.clipboard.writeText(
                  JSON.stringify(resumeData, null, 2)
                );
                alert("Resume data copied to clipboard!");
              }}
            >
              Copy to Clipboard
            </Button>
          </CardFooter>
        </Card>
      )}
    </div>
  );
}
```
