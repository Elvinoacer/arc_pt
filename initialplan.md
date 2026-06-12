# Product Requirements Document
## Architectural & Structural Engineer Portfolio Website

**Document Version:** 1.0  
**Status:** Draft  
**Last Updated:** June 2026

---

## 1. Executive Summary

This document defines the product requirements for a professional portfolio and design showcase website for a structural and architectural engineer. The platform serves a dual purpose: establishing a strong professional brand online and acting as a gated design marketplace where prospective clients can discover house plan designs and formally request access to purchase or license them.

The site must exude credibility, precision, and creative mastery — reflecting the engineer's professional identity — while providing a smooth and trustworthy experience for visitors who want to acquire his work.

---

## 2. Goals & Success Metrics

### 2.1 Business Goals

- Establish a strong, credible online presence for the engineer as an independent professional
- Create a central showcase for all completed and ongoing design work
- Generate qualified leads from serious buyers/clients through a structured inquiry system
- Enable the engineer to manage, update, and control his design catalog independently

### 2.2 Success Metrics

| Metric | Target |
|---|---|
| Monthly unique visitors (6 months post-launch) | ≥ 500 |
| Inquiry conversion rate (visitors → inquiry submission) | ≥ 3% |
| Average inquiry-to-response time (admin side) | < 24 hours |
| Mobile traffic usability score | ≥ 90 (Lighthouse) |
| Portfolio designs listed at launch | ≥ 10 |

---

## 3. Target Users

### 3.1 Primary Users

**Prospective Clients / Plan Buyers**
- Individuals planning to build homes in Kenya and East Africa
- Real estate developers looking for ready-made or customizable house plans
- Contractors and construction firms sourcing plans for their clients
- Diaspora Kenyans planning to build back home

**Profile:** Non-technical, mobile-first, trust-sensitive. They browse visually, compare designs, and need a low-friction way to reach out.

### 3.2 Secondary Users

**Professional Peers / Referrers**
- Fellow engineers, architects, and construction professionals who may refer clients or collaborate
- These users care about professional credentials, portfolio depth, and contact details

### 3.3 Admin User (The Engineer)
- Sole admin of the platform
- Uploads and manages design files, descriptions, previews, and metadata
- Receives and responds to inquiries
- No assumed high technical proficiency for day-to-day content management; the CMS/admin panel must be intuitive

---

## 4. Information Architecture

```
/                          → Landing / Home Page
/about                     → Professional Bio & Credentials
/portfolio                 → Design Showcase (browseable gallery)
/portfolio/[slug]          → Individual Design Detail Page
/inquire                   → Inquiry / Access Request Form
/contact                   → General Contact Page
/admin                     → Protected Admin Dashboard
/admin/designs             → Manage Design Listings
/admin/designs/new         → Upload New Design
/admin/designs/[id]/edit   → Edit Design
/admin/inquiries           → View & Manage Inquiries
/admin/inquiries/[id]      → Individual Inquiry Thread
```

---

## 5. Feature Specifications

---

### 5.1 Public-Facing Features

---

#### 5.1.1 Home Page (Landing)

**Purpose:** First impression. Convert a visitor from curious to engaged within seconds.

**Sections:**

1. **Hero Section**
   - Full-width, high-impact visual (featured design render or professional photograph)
   - Tagline: concise value proposition (e.g., _"Precision-Engineered House Plans for East Africa"_)
   - Two CTAs: `Browse Designs` (primary) and `Get In Touch` (secondary)

2. **About Snippet**
   - 2–3 sentence professional summary
   - Link to full `/about` page
   - Optional: professional headshot

3. **Featured Designs Carousel / Grid**
   - 3–6 hand-picked designs displayed as visual cards
   - Each card: design thumbnail, name, bedroom/bathroom count, brief tag (e.g., _"3 Bed | 2 Bath | Contemporary"_)
   - CTA per card: `View Design`
   - CTA below section: `See All Designs`

4. **Services / What I Offer**
   - Brief summary of what a client gets: residential plans, structural drawings, custom commissions, etc.
   - Icon + short description format (3–4 items)

5. **Process / How It Works**
   - Step-by-step visual explanation of how a client engages:
     1. Browse the catalog
     2. Submit an inquiry for a specific design
     3. Receive a response and access details
     4. Obtain the files / proceed with the project
   - Keeps expectations clear and reduces friction

6. **Testimonials / Social Proof** _(optional at launch, hooks present)_
   - Placeholder or real client quotes
   - Star ratings (if available)

7. **Call-to-Action Footer Banner**
   - Strong closing CTA: _"Have a project in mind? Let's talk."_ → `Start an Inquiry`

---

#### 5.1.2 About Page

**Purpose:** Build trust and credibility with the professional details of the engineer.

**Content Sections:**

1. **Professional Bio**
   - Photo (optional but recommended)
   - Full narrative background: education, years of experience, specializations
   - Philosophy or approach to design

2. **Credentials & Qualifications**
   - Degrees, professional certifications (e.g., EBK registration)
   - Professional memberships (IEK, AAK, etc.)
   - Notable projects or milestones

3. **Skills & Specializations**
   - Visual tags or grouped list: Residential Design, Structural Engineering, Site Planning, 3D Visualization, etc.

4. **Download CV** _(optional)_
   - A button to download a PDF version of his professional CV

5. **CTA**
   - Prompt toward the portfolio or inquiry form

---

#### 5.1.3 Portfolio / Design Showcase

**Purpose:** The core catalog. Visitors browse, filter, and discover designs.

**5.1.3.1 Portfolio Index Page (`/portfolio`)**

**Layout:** Responsive grid (3 columns on desktop, 2 on tablet, 1 on mobile)

**Each Design Card displays:**
- Primary preview image (rendered elevation or floor plan thumbnail)
- Design name / title
- Key metadata tags: plot size, number of bedrooms, number of bathrooms, style (e.g., _Modern_, _Bungalow_, _Maisonette_)
- Floor count
- Estimated build cost range _(optional)_
- `View Details` CTA button

**Filtering & Search:**
- Filter by: Design style, Number of bedrooms, Number of floors, Plot size range
- Search by: keyword (design name, style, tag)
- Sort by: Newest, Most Popular (most inquired), Featured first

**Pagination:** Infinite scroll or numbered pagination (configurable by admin)

---

**5.1.3.2 Individual Design Page (`/portfolio/[slug]`)**

This is the most critical page. It must convert a curious visitor into someone who submits an inquiry.

**Sections:**

1. **Design Gallery**
   - Full image gallery with lightbox: elevations, floor plans (ground floor, first floor), 3D renders if available
   - Primary large image with thumbnail strip below
   - Image labels (e.g., "Front Elevation", "Ground Floor Plan")

2. **Design Overview**
   - Design name and a short descriptive tagline
   - Key specs in a clean summary table:

     | Spec | Value |
     |---|---|
     | Design Style | Contemporary / Bungalow / etc. |
     | Plot Size Required | e.g., 50×100 ft |
     | Total Floor Area | e.g., 240 m² |
     | Bedrooms | 4 |
     | Bathrooms | 3 |
     | Floors | 2 |
     | Garage | Yes / No |
     | Estimated Build Cost | KES X – Y million |

3. **Full Description**
   - Rich text narrative describing the design concept, standout features, materials, and suitability

4. **Included in the Files** _(What they get on access)_
   - List of deliverables: Architectural drawings, Structural drawings, Plumbing layout, Electrical layout, Bill of Quantities (BOQ), etc.
   - File formats listed (e.g., PDF, DWG, Revit)
   - This section is key — it justifies the inquiry and sets clear expectations

5. **Gated Access Section**
   - A visually distinct "Access This Design" panel
   - Short compelling copy: _"Interested in building this design? Submit an inquiry to discuss access, pricing, and customization."_
   - Prominent CTA button: `Request Access to This Design`
   - Clicking this button navigates to `/inquire` with the design pre-selected

6. **Similar Designs**
   - 3–4 related design cards at the bottom (same style or similar bedroom count)

---

#### 5.1.4 Inquiry / Access Request Page (`/inquire`)

**Purpose:** The primary lead capture mechanism. Replaces a shopping cart. This form must feel professional, not bureaucratic.

**Form Fields:**

| Field | Type | Required |
|---|---|---|
| Full Name | Text | Yes |
| Email Address | Email | Yes |
| Phone Number | Tel | Yes |
| Country / Location | Select + Text | Yes |
| Design of Interest | Select (from catalog) or pre-filled if arriving from a design page | Yes |
| Nature of Interest | Radio: _"I want this exact design"_ / _"I want a customized version"_ / _"I want a fully custom design"_ | Yes |
| Plot Size (if known) | Text | No |
| Estimated Build Timeline | Select: _"Within 3 months" / "3–12 months" / "1–2 years" / "Just exploring"_ | No |
| Budget Range | Select: ranges in KES millions | No |
| Additional Message | Textarea | No |
| How did you hear about us? | Select | No |

**Post-Submission:**
- Thank-you confirmation page / inline success message
- Summary of what happens next: _"We'll review your inquiry and get back to you within 24 hours."_
- Email confirmation sent automatically to the client

**Validation:** Client-side and server-side validation on all required fields.

---

#### 5.1.5 Contact Page (`/contact`)

**Purpose:** General professional contact for non-design inquiries (collaborations, press, partnerships).

**Content:**
- Brief intro copy
- Contact form: Name, Email, Subject, Message
- Direct email address and phone number displayed
- Location (city, country — no full address needed)
- Social links: LinkedIn, Instagram (if used for design showcase), WhatsApp link

---

### 5.2 Admin Features

All admin routes are behind authentication. The engineer logs in with a single secure account.

---

#### 5.2.1 Admin Dashboard (`/admin`)

**Overview panel showing:**
- Total designs published
- Total inquiries (all time)
- New/unread inquiries (since last login)
- Quick links: `+ New Design`, `View Inquiries`

---

#### 5.2.2 Design Management (`/admin/designs`)

**Design Listing Table:**
- Columns: Thumbnail, Title, Style, Bedrooms, Status (Published / Draft), Inquiries count, Date created, Actions (Edit, Toggle Publish, Delete)
- Search and filter by status, style, date

**Create / Edit Design Form:**

| Field | Input Type | Notes |
|---|---|---|
| Title / Name | Text | |
| Slug | Auto-generated from title, editable | |
| Short Description | Textarea (plain text) | Used in cards |
| Full Description | Rich Text Editor (WYSIWYG) | For detail page |
| Design Style | Select / Tags | e.g., Contemporary, Bungalow |
| Number of Bedrooms | Number | |
| Number of Bathrooms | Number | |
| Number of Floors | Number | |
| Total Floor Area (m²) | Number | |
| Plot Size Required | Text | e.g., "50×100 ft" |
| Has Garage | Checkbox | |
| Estimated Build Cost (Min) | Number (KES) | Optional |
| Estimated Build Cost (Max) | Number (KES) | Optional |
| Files Included | Multi-line text / rich text | What the buyer gets |
| File Formats Available | Tags / text | e.g., PDF, DWG |
| Images | Multi-image upload | Drag-and-drop, re-orderable, label each image |
| Design Files (Actual) | File upload | Secure, not publicly accessible |
| Featured | Toggle | Appears in homepage section |
| Status | Toggle | Draft / Published |
| Tags / Keywords | Tags input | For search/filter |

**Image handling:**
- Upload multiple images per design
- Set a primary/cover image
- Label each image (e.g., "Front Elevation", "Floor Plan Level 1")
- Image previews in the editor

**File upload (design files):**
- Upload the actual architectural/structural files (PDF, DWG, Revit, etc.)
- Files are stored securely and are **never publicly accessible**
- Files are only shared with clients after approval, outside the platform (or via a future secure delivery feature)
- File names and sizes shown in admin for reference

---

#### 5.2.3 Inquiry Management (`/admin/inquiries`)

**Inquiries Table:**
- Columns: Client Name, Email, Design of Interest, Inquiry Type, Location, Submitted Date, Status, Action
- Status options: `New`, `Reviewed`, `Responded`, `Closed`
- Filterable by: Status, Design, Date range
- Sortable by: Date, Status

**Individual Inquiry View (`/admin/inquiries/[id]`):**
- Full inquiry details displayed in a clean card layout
- Client info: name, email, phone, location
- Design requested (with link to that design)
- All form answers
- Status dropdown (admin can change status)
- Notes field (private admin notes)
- Quick action buttons: `Mark as Responded`, `Mark as Closed`
- Direct "Reply via Email" button that opens a pre-filled mailto: link or sends an email via a configured email service

**Email Notifications:**
- Admin receives an email notification for every new inquiry
- Client receives an auto-reply email confirming receipt of their inquiry

---

#### 5.2.4 Admin Authentication

- Single admin account (no multi-user at launch)
- Secure login page at `/admin/login`
- Session management with secure cookies
- Password reset via email

---

## 6. Non-Functional Requirements

### 6.1 Performance
- Page load time (LCP) under 2.5 seconds on a 4G connection
- Images must be served in modern formats (WebP) and lazy-loaded
- Portfolio grid must handle up to 200 designs without performance degradation

### 6.2 Responsiveness & Accessibility
- Fully responsive: mobile, tablet, and desktop
- Touch-friendly gallery navigation
- Semantic HTML, keyboard navigability
- WCAG 2.1 AA compliance target
- Minimum touch target size: 44×44px

### 6.3 SEO
- Each design page has a unique, descriptive meta title and description
- Open Graph tags for social sharing (design images render when shared on WhatsApp, Facebook, etc.)
- Structured data (Schema.org) for portfolio items where applicable
- Sitemap.xml and robots.txt

### 6.4 Security
- Design files are never served via public URLs
- All form inputs sanitized server-side
- Admin routes protected with server-side session checks (no client-side-only guards)
- Rate limiting on the inquiry form to prevent spam
- HTTPS enforced

### 6.5 Scalability
- The data model must support up to 500 design listings without schema changes
- File storage should use a cloud provider (not local disk) to support scaling and backups

---

## 7. Data Models (Logical)

### Design

```
Design {
  id
  title
  slug                  (unique)
  shortDescription
  fullDescription       (rich text / HTML)
  style                 (e.g., "Contemporary", "Bungalow")
  bedrooms
  bathrooms
  floors
  floorArea             (m², float)
  plotSizeDescription   (text)
  hasGarage             (boolean)
  estimatedCostMin      (KES, optional)
  estimatedCostMax      (KES, optional)
  filesIncludedText     (rich text description of deliverables)
  fileFormats           (array of strings)
  isFeatured            (boolean)
  status                (DRAFT | PUBLISHED)
  tags                  (array of strings)
  images                (relation → DesignImage[])
  files                 (relation → DesignFile[])
  inquiries             (relation → Inquiry[])
  createdAt
  updatedAt
}
```

### DesignImage

```
DesignImage {
  id
  designId
  url                   (storage URL)
  label                 (e.g., "Front Elevation")
  isPrimary             (boolean)
  sortOrder             (integer)
  createdAt
}
```

### DesignFile

```
DesignFile {
  id
  designId
  fileName
  fileType              (e.g., "PDF", "DWG")
  storageKey            (internal cloud storage path, not public)
  fileSizeBytes
  uploadedAt
}
```

### Inquiry

```
Inquiry {
  id
  fullName
  email
  phone
  country
  designId              (relation → Design)
  designTitle           (denormalized, in case design is deleted)
  natureOfInterest      (EXACT | CUSTOMIZED | FULLY_CUSTOM)
  plotSize              (optional)
  buildTimeline         (optional)
  budgetRange           (optional)
  message               (optional)
  hearAboutUs           (optional)
  status                (NEW | REVIEWED | RESPONDED | CLOSED)
  adminNotes            (private)
  submittedAt
  updatedAt
}
```

### AdminUser

```
AdminUser {
  id
  email
  passwordHash
  createdAt
  lastLoginAt
}
```

---

## 8. User Flows

### 8.1 Visitor → Inquiry Submission

```
Home Page
  └─ Sees featured design → clicks "View Design"
      └─ Design Detail Page
          └─ Reviews images, specs, files included
              └─ Clicks "Request Access to This Design"
                  └─ Inquiry Form (design pre-filled)
                      └─ Fills in details → Submits
                          └─ Confirmation page shown
                          └─ Auto-reply email sent to client
                          └─ Notification email sent to admin
```

### 8.2 Admin → Responding to Inquiry

```
Admin logs in → Dashboard
  └─ Sees "3 new inquiries" badge
      └─ Goes to /admin/inquiries
          └─ Opens inquiry → Reviews client details + design requested
              └─ Changes status to "Reviewed"
              └─ Clicks "Reply via Email" → responds outside the platform
              └─ Changes status to "Responded"
              └─ Adds private note for record-keeping
```

### 8.3 Admin → Publishing a New Design

```
Admin logs in → Dashboard
  └─ Clicks "+ New Design"
      └─ Fills in all design metadata
      └─ Uploads preview images, sets primary image, labels each
      └─ Uploads actual design files (secure)
      └─ Saves as Draft → previews on the site
      └─ Clicks "Publish" → design appears in public portfolio
```

---

## 9. Content Requirements

The following real content must be provided by the engineer before launch:

- [ ] Professional bio (200–400 words)
- [ ] Professional headshot (optional but recommended)
- [ ] List of credentials / certifications
- [ ] Minimum 10 design listings with:
  - [ ] At least 2 high-quality preview images per design
  - [ ] Accurate metadata (beds, baths, area, style)
  - [ ] Description of what files are included
  - [ ] Short and full description text
- [ ] Services / What I Offer copy (3–5 items)
- [ ] Tagline / value proposition for the hero section
- [ ] Contact details (email, phone, optional WhatsApp)
- [ ] Social media links (if applicable)

---

## 10. Out of Scope (V1)

The following features are intentionally excluded from the initial version but should be considered for future phases:

| Feature | Notes |
|---|---|
| Online payments / e-commerce checkout | V1 uses inquiry-based lead capture; payment handled offline |
| Client login / account portal | Future: clients log in to see their inquiry status and download files |
| Secure in-platform file delivery | Future: approved clients get a time-limited download link |
| Blog / articles section | Future: SEO content marketing |
| Multi-admin / team accounts | Not needed at launch |
| Live chat / chatbot | Future consideration |
| Design comparison tool | Nice-to-have for future |
| Custom project quoting tool | Future: interactive cost estimator |
| Multi-language support | Future: Swahili version |

---

## 11. Appendix: Glossary

| Term | Definition |
|---|---|
| Design / House Plan | A complete set of architectural and structural drawings for a specific building |
| Inquiry | A formal access request submitted by a prospective client through the website |
| Gated Access | The model where design files are not directly downloadable; access is given after an inquiry and approval |
| Slug | A URL-friendly identifier for a design page (e.g., `/portfolio/4-bedroom-maisonette-nakuru`) |
| Draft | A design that is saved but not visible to the public |
| Published | A design that is visible in the public portfolio |

---

*End of Document*