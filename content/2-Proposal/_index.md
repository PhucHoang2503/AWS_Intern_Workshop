---
title: "Proposal"
date: 2025-09-09
weight: 2
chapter: false
pre: " <b> 2. </b> "
---

# SnapResume Platform

## 1\. Project Summary

SnapResume is an intelligent, AI-powered resume-building platform designed to help job seekers create professional, ATS (Applicant Tracking System) compliant resumes quickly and efficiently. The platform offers modern design templates, intuitive editing tools, and a smart recommendation system that optimizes content to match job descriptions.

The key differentiator is the integration of an AI Search & Recommendation Engine using **AWS Bedrock** (Claude 3 Sonnet). This allows the system to automatically analyze job descriptions and suggest the most relevant resume sections from the user's experience data repository. The system is built entirely on a Serverless architecture, ensuring infinite scalability and optimized costs.

## 2\. Problem Statement

### What is the problem?

- **Formatting Difficulties:** Job seekers often spend hours formatting text in Word or complex design tools without achieving a professional look.
- **Difficulty in Selecting Information:** Candidates often have extensive experience but struggle to select what is most relevant to a specific position. Sending a "generic" CV to every company reduces the chance of acceptance.
- **ATS Rejection:** Manually designed resumes often contain graphic elements or table structures that make them unreadable to employers' automated Applicant Tracking Systems (ATS).

### The Solution

SnapResume addresses these issues with a modern React web application built on the AWS Serverless platform.

- **Visual Editor:** Simple drag-and-drop or form-filling interface with Real-time Preview.
- **AI Smart Matcher:** Uses **AWS Bedrock** to analyze the relevance between the candidate's experience and job requirements, thereby recommending a list of skills and projects to include in the CV.
- **ATS Compliant:** Templates are designed to be machine-friendly while remaining aesthetically pleasing for human readers.
- **Centralized Management:** Stores a library of sections and allows for "assembling" multiple resume versions based on specific needs.

## 3\. Solution Architecture

The system utilizes Serverless and Event-driven architecture on AWS.
![AWS Architecture](/images/2-Proposal/SnapResume.png)

### Architecture Overview

- **Frontend:** React + Vite + Ant Design (hosted on Amplify and accelerated via CloudFront).
- **API Layer:** Amazon API Gateway + AWS Lambda (Node.js/TypeScript).
- **Database:** Amazon DynamoDB for storing Users, Resumes, Sections, and Templates.
- **Authentication:** Amazon Cognito (User Pools & Identity Pools).
- **AI Engine:** Amazon Bedrock (Claude 3 Sonnet) for analysis and recommendation tasks.
- **Storage:** Amazon S3 for storing profile pictures and PDF files.

## 4\. Technical Implementation

### Technology Stack

- **Frontend:** ReactJS, Ant Design, CSS Modules, html2pdf.js for PDF export.
- **Backend:** NodeJS, TypeScript, Express (running inside Lambda).
- **IaC:** Terraform for managing the entire infrastructure.

### Phases

1.  **Phase 1: Core Foundation** (Completed)
    - Setup AWS Infrastructure (Terraform).
    - Build Authentication (Cognito).
    - Basic CRUD for Resumes & Sections (DynamoDB + Lambda).
2.  **Phase 2: Editor & Templates** (Completed)
    - Develop Universal Editor Interface (Extension/Web).
    - Build Dynamic Template System.
    - PDF Export Feature (html2pdf.js).
3.  **Phase 3: AI Integration & Polish** (In Progress)
    - Integrate Amazon Bedrock for "AI Recommendation" feature.
    - Optimize UX/UI (Features Page, Editor Flow).
    - Extension Integration (Web Clipper flow).

## 5\. Budget Estimate (AWS Costs)

The Serverless pricing model ensures costs are proportional to usage. AI costs are focused on analyzing large input data (Input Tokens).

### AI Cost Assumptions

The estimated costs for the Amazon Bedrock service (utilizing the Claude 3 Sonnet model) are calculated based on the following _real-world usage scenario_ (MVP):

#### 1. Usage Frequency & Volume

1.  **Processing Rate**: The system processes an average of 1 request per minute (1 request/minute).
2.  **Operating Time**: The system is expected to be under high load or actively used for 2 hours/day (120 minutes/day).
3.  **Total Monthly Volume**: 120 requests/day × 30 days = 3,600 requests/month.

#### 2. Data Size (Token Size) per Request

1.  **Input (Input Tokens)**: Average of 500 tokens/request.
    - Includes: Data extracted from the user's current CV and the content of the Job Description to be analyzed.
2.  **Output (Output Tokens)**: Average of 600 tokens/request.
    - Includes: Analysis results, editing suggestions, and data returned in structured JSON format.

#### 3. Unit Pricing (Pricing - Claude 3 Sonnet)

- **Input**: $0.003 / 1,000 tokens (equivalent to $3.00 / 1 million tokens).
- **Output**: $0.015 / 1,000 tokens (equivalent to $15.00 / 1 million tokens).

#### AI Cost Breakdown

- Total Monthly Volume = Frequency _ Duration _ 30 days
- Input Token Cost = Total Monthly Volume _ Input Tokens per Request _ ($3 / 1 million tokens)
- Output Token Cost = Total Monthly Volume _ Output Tokens per Request _ ($15 / 1 million tokens)
- Total Cost = Input Token Cost + Output Token Cost

##### Low traffic

- Processing Rate: 1 request/minute
- Operating Time: 2 hours/day

##### High traffic

- Processing Rate: 2 request/minute
- Operating Time: 3 hours/day

| Service                | Pricing Model | Low Traffic (MVP) | Medium Traffic |
| :--------------------- | :------------ | :---------------- | :------------- |
| Amazon S3              | Storage       | $0.28             | $0.77          |
| CloudFront             | CDN           | Free Tier         | Free Tier      |
| API GW + Lambda        | Compute       | Free Tier         | $2.80          |
| DynamoDB               | Database      | Free Tier         | $6.25          |
| Cognito                | Auth          | Free Tier         | Free Tier      |
| Amazon Bedrock (AI)    | Tokens        | $37.8             | $113.4         |
| WAF + Route53          | Security      | $12.60            | $12.60         |
| **Total Cost / Month** |               | **\~ $50.68**     | **\~ $135.82** |

## 6\. Risk Assessment

### Security & Privacy Risks (High)

- **Issue:** The Recommendation feature requires sending the user's entire Section data to Bedrock for analysis.
- **Mitigation:**
  - AWS Bedrock complies with regulations ensuring customer data is not used to train base models.
  - Data encryption at rest in DynamoDB.

### Cost Risks (Medium)

- **Issue:** Users spamming the "Recommend" button with long Job Descriptions causing high Input Token usage.
- **Mitigation:**
  - Limit the length of Job Description Input.
  - Implement Quota limits (e.g., 10 analysis calls/day for Free accounts).

### Architectural Risks (Low)

- **Issue:** Lambda cold starts slowing down the initial user experience.
- **Mitigation:** Use Lambda SnapStart (for Java) or Provisioned Concurrency if necessary (though Node.js cold starts are generally quite fast).

## 7\. Expected Outcomes

- Ownership of a complete SaaS platform for the recruitment market.
- Solving the "Matching" problem between candidates and jobs, rather than being just a pure text editing tool.
- Leveraging AWS AI power to create real value (Smart Search) instead of generic "writing assistance" features.

[Attached Documents](https://drive.google.com/drive/folders/1zQJ8XC6bdMWveQYIaCO-RkHh9Ppy1WyE?usp=sharing)
