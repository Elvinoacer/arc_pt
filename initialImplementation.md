# Implementation Plan
## Architectural & Structural Engineer Portfolio Website

**Document Version:** 1.0  
**Stack:** Next.js 16+ · Prisma · PostgreSQL · TypeScript  
**Status:** Ready for Development  
**Last Updated:** June 2026

---

## Overview

This document is the engineering playbook for building the portfolio platform defined in the PRD. It is broken into **5 phases**, each independently shippable and testable. Phases build on each other; nothing in a later phase will require revisiting the architecture of an earlier one.

```
Phase 1 → Project Foundation & Infrastructure        (~3–5 days)
Phase 2 → Database, Models & Storage Layer           (~3–4 days)
Phase 3 → Public-Facing UI                           (~6–9 days)
Phase 4 → Admin Panel                                (~5–7 days)
Phase 5 → Polish, SEO, Notifications & Launch        (~3–4 days)
```

**Total estimated timeline:** 20–29 focused working days

---

## Phase 1: Project Foundation & Infrastructure

> Goal: A clean, well-configured Next.js project that the entire team can build on with zero ambiguity. No features yet — just solid ground.

---

### 1.1 Repository & Project Bootstrap

**Tasks:**

- Initialize Next.js project with the App Router and TypeScript strict mode enabled
  ```bash
  npx create-next-app@latest portfolio --typescript --app --eslint --tailwind --src-dir
  ```
- Configure `tsconfig.json` with path aliases:
  ```json
  {
    "compilerOptions": {
      "baseUrl": ".",
      "paths": {
        "@/*": ["./src/*"],
        "@/components/*": ["./src/components/*"],
        "@/lib/*": ["./src/lib/*"],
        "@/types/*": ["./src/types/*"]
      }
    }
  }
  ```
- Set up `.env.local` template with all required environment variable keys (values blank):
  ```env
  DATABASE_URL=
  DIRECT_URL=
  NEXTAUTH_SECRET=
  NEXTAUTH_URL=
  CLOUDINARY_CLOUD_NAME=
  CLOUDINARY_API_KEY=
  CLOUDINARY_API_SECRET=
  RESEND_API_KEY=
  ADMIN_EMAIL=
  ```
- Initialize Git repository with a `.gitignore` that excludes `.env*`, `node_modules`, `.next`, and `/uploads`
- Create a `README.md` documenting local setup steps

---

### 1.2 Folder Structure

Establish the following structure upfront — do not deviate mid-project:

```
src/
├── app/
│   ├── (public)/               # Public route group
│   │   ├── page.tsx            # Home
│   │   ├── about/
│   │   │   └── page.tsx
│   │   ├── portfolio/
│   │   │   ├── page.tsx        # Portfolio index
│   │   │   └── [slug]/
│   │   │       └── page.tsx    # Design detail
│   │   ├── inquire/
│   │   │   └── page.tsx
│   │   └── contact/
│   │       └── page.tsx
│   ├── (admin)/                # Admin route group
│   │   ├── admin/
│   │   │   ├── page.tsx        # Dashboard
│   │   │   ├── designs/
│   │   │   │   ├── page.tsx
│   │   │   │   ├── new/
│   │   │   │   │   └── page.tsx
│   │   │   │   └── [id]/
│   │   │   │       └── edit/
│   │   │   │           └── page.tsx
│   │   │   └── inquiries/
│   │   │       ├── page.tsx
│   │   │       └── [id]/
│   │   │           └── page.tsx
│   │   └── login/
│   │       └── page.tsx
│   ├── api/
│   │   ├── auth/
│   │   │   └── [...nextauth]/
│   │   │       └── route.ts
│   │   ├── designs/
│   │   │   ├── route.ts        # GET all, POST create
│   │   │   └── [id]/
│   │   │       └── route.ts    # GET one, PATCH, DELETE
│   │   ├── inquiries/
│   │   │   ├── route.ts        # GET all, POST create
│   │   │   └── [id]/
│   │   │       └── route.ts    # GET one, PATCH
│   │   └── upload/
│   │       └── route.ts        # File/image upload handler
│   ├── layout.tsx
│   └── globals.css
├── components/
│   ├── ui/                     # Primitive UI components
│   │   ├── Button.tsx
│   │   ├── Badge.tsx
│   │   ├── Card.tsx
│   │   ├── Input.tsx
│   │   ├── Select.tsx
│   │   ├── Textarea.tsx
│   │   ├── Modal.tsx
│   │   └── Spinner.tsx
│   ├── layout/
│   │   ├── Navbar.tsx
│   │   ├── Footer.tsx
│   │   └── AdminSidebar.tsx
│   ├── portfolio/
│   │   ├── DesignCard.tsx
│   │   ├── DesignGallery.tsx
│   │   ├── DesignFilters.tsx
│   │   └── SimilarDesigns.tsx
│   ├── home/
│   │   ├── HeroSection.tsx
│   │   ├── FeaturedDesigns.tsx
│   │   ├── ServicesSection.tsx
│   │   ├── HowItWorks.tsx
│   │   └── TestimonialsSection.tsx
│   ├── inquiry/
│   │   └── InquiryForm.tsx
│   └── admin/
│       ├── DesignForm.tsx
│       ├── ImageUploader.tsx
│       ├── FileUploader.tsx
│       └── InquiryTable.tsx
├── lib/
│   ├── prisma.ts               # Prisma client singleton
│   ├── auth.ts                 # NextAuth config
│   ├── cloudinary.ts           # Cloudinary helpers
│   ├── email.ts                # Resend/email helpers
│   ├── utils.ts                # General utilities
│   └── validations.ts          # Zod schemas
├── types/
│   └── index.ts                # Shared TypeScript types
└── hooks/
    ├── useDesigns.ts
    └── useInquiries.ts
```

---

### 1.3 Styling System

- Tailwind CSS is the primary styling tool (already installed via `create-next-app`)
- Define a design token set in `tailwind.config.ts` before writing a single component:

```ts
// tailwind.config.ts
const config = {
  theme: {
    extend: {
      colors: {
        brand: {
          primary: '#1B2B4B',     // Deep navy — authority, precision
          accent: '#C8A96E',      // Warm gold — premium, craftsmanship
          surface: '#F8F6F2',     // Off-white — clean, architectural
          muted: '#6B7280',       // Gray for secondary text
          dark: '#0F1923',        // Near-black for headings
        },
      },
      fontFamily: {
        heading: ['var(--font-playfair)', 'serif'],
        body: ['var(--font-inter)', 'sans-serif'],
      },
      borderRadius: {
        card: '0.75rem',
      },
    },
  },
};
```

- Load fonts in `layout.tsx` using `next/font/google`: **Playfair Display** (headings) and **Inter** (body)
- Define base global styles in `globals.css`: heading styles, body default, focus rings, scrollbar styling

---

### 1.4 Core Dependencies Installation

Install all third-party packages in one shot to avoid version conflicts:

```bash
# ORM & Auth
npm install prisma @prisma/client next-auth@beta

# Forms & Validation
npm install react-hook-form @hookform/resolvers zod

# Image & File Handling
npm install cloudinary next-cloudinary

# Rich Text Editor
npm install @tiptap/react @tiptap/starter-kit @tiptap/extension-image

# Email
npm install resend

# UI Utilities
npm install clsx tailwind-merge lucide-react

# Lightbox / Gallery
npm install yet-another-react-lightbox

# Drag & Drop (image reordering)
npm install @dnd-kit/core @dnd-kit/sortable

# Dev tools
npm install --save-dev @types/node prettier eslint-config-prettier
```

---

### 1.5 Environment & Linting Configuration

- Configure ESLint with the Next.js recommended ruleset + prettier
- Add `prettier.config.js`:
  ```js
  module.exports = {
    semi: true,
    singleQuote: true,
    trailingComma: 'es5',
    printWidth: 100,
    tabWidth: 2,
  };
  ```
- Add npm scripts to `package.json`:
  ```json
  {
    "scripts": {
      "dev": "next dev",
      "build": "next build",
      "start": "next start",
      "lint": "next lint",
      "format": "prettier --write .",
      "db:push": "prisma db push",
      "db:studio": "prisma studio",
      "db:seed": "npx ts-node prisma/seed.ts"
    }
  }
  ```

---

### Phase 1 Deliverables Checklist

- [ ] Next.js 16+ project initialized with TypeScript, App Router, Tailwind
- [ ] Complete folder structure in place (empty files are fine)
- [ ] Design tokens and font config in `tailwind.config.ts`
- [ ] All npm packages installed
- [ ] `.env.local` template committed (no secrets)
- [ ] ESLint + Prettier configured
- [ ] `README.md` with local setup instructions
- [ ] Pushes to Git without errors (`npm run lint` passes)

---

## Phase 2: Database, Models & Storage Layer

> Goal: A complete, working data layer. By the end of this phase you can create, read, update, and delete all entities via the database — no UI yet.

---

### 2.1 Prisma Schema

Create `prisma/schema.prisma`:

```prisma
generator client {
  provider = "prisma-client-js"
}

datasource db {
  provider  = "postgresql"
  url       = env("DATABASE_URL")
  directUrl = env("DIRECT_URL")
}

// ─── Enums ───────────────────────────────────────────────

enum DesignStatus {
  DRAFT
  PUBLISHED
}

enum NatureOfInterest {
  EXACT
  CUSTOMIZED
  FULLY_CUSTOM
}

enum InquiryStatus {
  NEW
  REVIEWED
  RESPONDED
  CLOSED
}

// ─── Models ──────────────────────────────────────────────

model Design {
  id                  String        @id @default(cuid())
  title               String
  slug                String        @unique
  shortDescription    String
  fullDescription     String        @db.Text
  style               String
  bedrooms            Int
  bathrooms           Int
  floors              Int
  floorArea           Float
  plotSizeDescription String
  hasGarage           Boolean       @default(false)
  estimatedCostMin    Int?
  estimatedCostMax    Int?
  filesIncludedText   String        @db.Text
  fileFormats         String[]
  isFeatured          Boolean       @default(false)
  status              DesignStatus  @default(DRAFT)
  tags                String[]
  viewCount           Int           @default(0)
  createdAt           DateTime      @default(now())
  updatedAt           DateTime      @updatedAt

  images              DesignImage[]
  files               DesignFile[]
  inquiries           Inquiry[]

  @@index([status])
  @@index([slug])
  @@index([style])
  @@index([isFeatured])
}

model DesignImage {
  id        String   @id @default(cuid())
  designId  String
  url       String
  publicId  String   // Cloudinary public_id for deletion
  label     String
  isPrimary Boolean  @default(false)
  sortOrder Int      @default(0)
  width     Int?
  height    Int?
  createdAt DateTime @default(now())

  design    Design   @relation(fields: [designId], references: [id], onDelete: Cascade)

  @@index([designId])
}

model DesignFile {
  id           String   @id @default(cuid())
  designId     String
  fileName     String
  fileType     String
  storageKey   String   // Cloudinary secure asset path — NEVER exposed publicly
  fileSizeBytes Int
  uploadedAt   DateTime @default(now())

  design       Design   @relation(fields: [designId], references: [id], onDelete: Cascade)

  @@index([designId])
}

model Inquiry {
  id               String           @id @default(cuid())
  fullName         String
  email            String
  phone            String
  country          String
  designId         String?
  designTitle      String           // Denormalized — survives design deletion
  natureOfInterest NatureOfInterest
  plotSize         String?
  buildTimeline    String?
  budgetRange      String?
  message          String?          @db.Text
  hearAboutUs      String?
  status           InquiryStatus    @default(NEW)
  adminNotes       String?          @db.Text
  submittedAt      DateTime         @default(now())
  updatedAt        DateTime         @updatedAt

  design           Design?          @relation(fields: [designId], references: [id], onDelete: SetNull)

  @@index([status])
  @@index([designId])
  @@index([submittedAt])
}

model AdminUser {
  id          String    @id @default(cuid())
  email       String    @unique
  passwordHash String
  createdAt   DateTime  @default(now())
  lastLoginAt DateTime?
}
```

Run migrations:
```bash
npx prisma db push          # Development (no migration files)
# OR
npx prisma migrate dev --name init   # Production-ready (creates migration files — preferred)
```

---

### 2.2 Prisma Client Singleton

`src/lib/prisma.ts`:
```ts
import { PrismaClient } from '@prisma/client';

const globalForPrisma = globalThis as unknown as { prisma: PrismaClient };

export const prisma =
  globalForPrisma.prisma ??
  new PrismaClient({
    log: process.env.NODE_ENV === 'development' ? ['query', 'error', 'warn'] : ['error'],
  });

if (process.env.NODE_ENV !== 'production') globalForPrisma.prisma = prisma;
```

---

### 2.3 Zod Validation Schemas

`src/lib/validations.ts` — define all schemas used by API routes and forms:

```ts
import { z } from 'zod';

export const designSchema = z.object({
  title: z.string().min(3).max(120),
  shortDescription: z.string().min(20).max(300),
  fullDescription: z.string().min(50),
  style: z.string().min(2),
  bedrooms: z.number().int().min(1).max(20),
  bathrooms: z.number().int().min(1).max(20),
  floors: z.number().int().min(1).max(10),
  floorArea: z.number().positive(),
  plotSizeDescription: z.string().min(2),
  hasGarage: z.boolean().default(false),
  estimatedCostMin: z.number().int().positive().optional(),
  estimatedCostMax: z.number().int().positive().optional(),
  filesIncludedText: z.string().min(10),
  fileFormats: z.array(z.string()).min(1),
  isFeatured: z.boolean().default(false),
  status: z.enum(['DRAFT', 'PUBLISHED']).default('DRAFT'),
  tags: z.array(z.string()),
});

export const inquirySchema = z.object({
  fullName: z.string().min(2).max(100),
  email: z.string().email(),
  phone: z.string().min(7).max(20),
  country: z.string().min(2),
  designId: z.string().optional(),
  designTitle: z.string().min(1),
  natureOfInterest: z.enum(['EXACT', 'CUSTOMIZED', 'FULLY_CUSTOM']),
  plotSize: z.string().optional(),
  buildTimeline: z.string().optional(),
  budgetRange: z.string().optional(),
  message: z.string().max(2000).optional(),
  hearAboutUs: z.string().optional(),
});

export const contactSchema = z.object({
  name: z.string().min(2),
  email: z.string().email(),
  subject: z.string().min(2),
  message: z.string().min(10).max(2000),
});
```

---

### 2.4 Cloudinary Storage Setup

`src/lib/cloudinary.ts`:

```ts
import { v2 as cloudinary } from 'cloudinary';

cloudinary.config({
  cloud_name: process.env.CLOUDINARY_CLOUD_NAME,
  api_key: process.env.CLOUDINARY_API_KEY,
  api_secret: process.env.CLOUDINARY_API_SECRET,
  secure: true,
});

/**
 * Upload a design preview image — publicly accessible
 */
export async function uploadDesignImage(file: Buffer, fileName: string) {
  return new Promise<{ url: string; publicId: string; width: number; height: number }>(
    (resolve, reject) => {
      cloudinary.uploader
        .upload_stream(
          {
            folder: 'portfolio/images',
            public_id: fileName,
            resource_type: 'image',
            format: 'webp',
            transformation: [{ quality: 'auto', fetch_format: 'auto' }],
          },
          (error, result) => {
            if (error || !result) return reject(error);
            resolve({
              url: result.secure_url,
              publicId: result.public_id,
              width: result.width,
              height: result.height,
            });
          }
        )
        .end(file);
    }
  );
}

/**
 * Upload actual design files — stored in a PRIVATE folder, never publicly URL-accessible
 */
export async function uploadDesignFile(file: Buffer, fileName: string) {
  return new Promise<{ storageKey: string; bytes: number }>((resolve, reject) => {
    cloudinary.uploader
      .upload_stream(
        {
          folder: 'portfolio/files',        // This folder should be set to authenticated delivery
          public_id: fileName,
          resource_type: 'raw',
          type: 'authenticated',            // Requires signed URL to access — never public
        },
        (error, result) => {
          if (error || !result) return reject(error);
          resolve({
            storageKey: result.public_id,
            bytes: result.bytes,
          });
        }
      )
      .end(file);
  });
}

/**
 * Delete an image from Cloudinary by its publicId
 */
export async function deleteCloudinaryAsset(publicId: string, resourceType = 'image') {
  return cloudinary.uploader.destroy(publicId, { resource_type: resourceType as any });
}
```

---

### 2.5 API Routes — Data Layer

Build all API routes as thin handlers: validate input → call Prisma → return JSON.

#### `POST /api/designs` — Create Design

```ts
// src/app/api/designs/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import { designSchema } from '@/lib/validations';
import { requireAdmin } from '@/lib/auth';

export async function GET(req: NextRequest) {
  const { searchParams } = new URL(req.url);
  const status = searchParams.get('status') ?? 'PUBLISHED';
  const style = searchParams.get('style');
  const bedrooms = searchParams.get('bedrooms');
  const featured = searchParams.get('featured');
  const search = searchParams.get('q');
  const page = parseInt(searchParams.get('page') ?? '1');
  const limit = parseInt(searchParams.get('limit') ?? '12');

  const where: any = { status };
  if (style) where.style = style;
  if (bedrooms) where.bedrooms = parseInt(bedrooms);
  if (featured === 'true') where.isFeatured = true;
  if (search) {
    where.OR = [
      { title: { contains: search, mode: 'insensitive' } },
      { tags: { has: search } },
      { style: { contains: search, mode: 'insensitive' } },
    ];
  }

  const [designs, total] = await Promise.all([
    prisma.design.findMany({
      where,
      include: {
        images: { where: { isPrimary: true }, take: 1 },
        _count: { select: { inquiries: true } },
      },
      orderBy: [{ isFeatured: 'desc' }, { createdAt: 'desc' }],
      skip: (page - 1) * limit,
      take: limit,
    }),
    prisma.design.count({ where }),
  ]);

  return NextResponse.json({ designs, total, page, pages: Math.ceil(total / limit) });
}

export async function POST(req: NextRequest) {
  await requireAdmin(req);

  const body = await req.json();
  const parsed = designSchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json({ error: parsed.error.flatten() }, { status: 400 });
  }

  const slug = body.title
    .toLowerCase()
    .replace(/[^a-z0-9\s-]/g, '')
    .replace(/\s+/g, '-')
    .slice(0, 80);

  const design = await prisma.design.create({
    data: { ...parsed.data, slug },
  });

  return NextResponse.json(design, { status: 201 });
}
```

#### `POST /api/inquiries` — Submit Inquiry

```ts
// src/app/api/inquiries/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { prisma } from '@/lib/prisma';
import { inquirySchema } from '@/lib/validations';
import { sendInquiryNotification, sendInquiryConfirmation } from '@/lib/email';

export async function POST(req: NextRequest) {
  const body = await req.json();
  const parsed = inquirySchema.safeParse(body);
  if (!parsed.success) {
    return NextResponse.json({ error: parsed.error.flatten() }, { status: 400 });
  }

  const inquiry = await prisma.inquiry.create({ data: parsed.data });

  // Fire-and-forget email notifications (don't block response)
  Promise.all([
    sendInquiryNotification(inquiry),       // To admin
    sendInquiryConfirmation(inquiry),       // To client
  ]).catch(console.error);

  return NextResponse.json({ success: true, id: inquiry.id }, { status: 201 });
}
```

#### `PATCH /api/inquiries/[id]` — Update Status / Notes

```ts
export async function PATCH(req: NextRequest, { params }: { params: { id: string } }) {
  await requireAdmin(req);

  const { status, adminNotes } = await req.json();

  const inquiry = await prisma.inquiry.update({
    where: { id: params.id },
    data: {
      ...(status && { status }),
      ...(adminNotes !== undefined && { adminNotes }),
    },
  });

  return NextResponse.json(inquiry);
}
```

#### `POST /api/upload` — Image & File Upload

```ts
// src/app/api/upload/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { uploadDesignImage, uploadDesignFile } from '@/lib/cloudinary';
import { requireAdmin } from '@/lib/auth';

export async function POST(req: NextRequest) {
  await requireAdmin(req);

  const formData = await req.formData();
  const file = formData.get('file') as File;
  const type = formData.get('type') as 'image' | 'file';

  if (!file) return NextResponse.json({ error: 'No file provided' }, { status: 400 });

  const buffer = Buffer.from(await file.arrayBuffer());
  const fileName = `${Date.now()}-${file.name.replace(/[^a-zA-Z0-9._-]/g, '')}`;

  if (type === 'image') {
    const result = await uploadDesignImage(buffer, fileName);
    return NextResponse.json(result);
  } else {
    const result = await uploadDesignFile(buffer, fileName);
    return NextResponse.json({ ...result, fileName: file.name, fileType: file.type });
  }
}
```

---

### 2.6 Authentication Setup

`src/lib/auth.ts` — NextAuth v5 (beta) configuration:

```ts
import NextAuth from 'next-auth';
import Credentials from 'next-auth/providers/credentials';
import bcrypt from 'bcryptjs';
import { prisma } from '@/lib/prisma';

export const { handlers, auth, signIn, signOut } = NextAuth({
  providers: [
    Credentials({
      credentials: {
        email: { label: 'Email', type: 'email' },
        password: { label: 'Password', type: 'password' },
      },
      async authorize(credentials) {
        if (!credentials?.email || !credentials?.password) return null;

        const admin = await prisma.adminUser.findUnique({
          where: { email: credentials.email as string },
        });

        if (!admin) return null;

        const passwordMatch = await bcrypt.compare(
          credentials.password as string,
          admin.passwordHash
        );

        if (!passwordMatch) return null;

        await prisma.adminUser.update({
          where: { id: admin.id },
          data: { lastLoginAt: new Date() },
        });

        return { id: admin.id, email: admin.email };
      },
    }),
  ],
  pages: { signIn: '/admin/login' },
  session: { strategy: 'jwt' },
  callbacks: {
    authorized({ auth, request: { nextUrl } }) {
      const isAdminRoute = nextUrl.pathname.startsWith('/admin');
      const isLoggedIn = !!auth?.user;
      if (isAdminRoute && !isLoggedIn) return false;
      return true;
    },
  },
});

// Middleware helper — call in API routes that require admin
export async function requireAdmin(req: Request) {
  const session = await auth();
  if (!session?.user) {
    throw new Response(JSON.stringify({ error: 'Unauthorized' }), { status: 401 });
  }
  return session.user;
}
```

Create `middleware.ts` at the project root:
```ts
export { auth as middleware } from '@/lib/auth';

export const config = {
  matcher: ['/admin/:path*', '/api/designs/:path*', '/api/inquiries/:path*', '/api/upload/:path*'],
};
```

---

### 2.7 Email Service

`src/lib/email.ts` — using Resend:

```ts
import { Resend } from 'resend';
import type { Inquiry } from '@prisma/client';

const resend = new Resend(process.env.RESEND_API_KEY);
const ADMIN_EMAIL = process.env.ADMIN_EMAIL!;
const FROM_EMAIL = 'noreply@yourdomain.com';

export async function sendInquiryNotification(inquiry: Inquiry) {
  await resend.emails.send({
    from: FROM_EMAIL,
    to: ADMIN_EMAIL,
    subject: `New Inquiry: ${inquiry.designTitle} — ${inquiry.fullName}`,
    html: `
      <h2>New Design Inquiry Received</h2>
      <table>
        <tr><td><strong>Name:</strong></td><td>${inquiry.fullName}</td></tr>
        <tr><td><strong>Email:</strong></td><td>${inquiry.email}</td></tr>
        <tr><td><strong>Phone:</strong></td><td>${inquiry.phone}</td></tr>
        <tr><td><strong>Country:</strong></td><td>${inquiry.country}</td></tr>
        <tr><td><strong>Design:</strong></td><td>${inquiry.designTitle}</td></tr>
        <tr><td><strong>Interest Type:</strong></td><td>${inquiry.natureOfInterest}</td></tr>
        <tr><td><strong>Timeline:</strong></td><td>${inquiry.buildTimeline ?? 'Not specified'}</td></tr>
        <tr><td><strong>Budget:</strong></td><td>${inquiry.budgetRange ?? 'Not specified'}</td></tr>
        <tr><td><strong>Message:</strong></td><td>${inquiry.message ?? 'None'}</td></tr>
      </table>
      <p><a href="${process.env.NEXTAUTH_URL}/admin/inquiries/${inquiry.id}">View in Admin Panel →</a></p>
    `,
  });
}

export async function sendInquiryConfirmation(inquiry: Inquiry) {
  await resend.emails.send({
    from: FROM_EMAIL,
    to: inquiry.email,
    subject: `We've received your inquiry — ${inquiry.designTitle}`,
    html: `
      <h2>Thank you, ${inquiry.fullName}!</h2>
      <p>We have received your inquiry for <strong>${inquiry.designTitle}</strong> and will review it shortly.</p>
      <p>You can expect a response within <strong>24 hours</strong> to the email address you provided.</p>
      <p>If you have any immediate questions, feel free to reply to this email.</p>
      <br />
      <p>Best regards,<br />[Engineer Name]<br />Structural & Architectural Engineer</p>
    `,
  });
}
```

---

### 2.8 Database Seed Script

`prisma/seed.ts` — creates the initial admin user and 2–3 sample designs for development:

```ts
import { PrismaClient } from '@prisma/client';
import bcrypt from 'bcryptjs';

const prisma = new PrismaClient();

async function main() {
  // Create admin user
  const passwordHash = await bcrypt.hash('changeme123', 12);
  await prisma.adminUser.upsert({
    where: { email: 'admin@example.com' },
    update: {},
    create: { email: 'admin@example.com', passwordHash },
  });

  // Create sample designs
  await prisma.design.createMany({
    skipDuplicates: true,
    data: [
      {
        title: '4-Bedroom Maisonette',
        slug: '4-bedroom-maisonette',
        shortDescription: 'A modern two-storey maisonette with open-plan living spaces.',
        fullDescription: '<p>Full description here...</p>',
        style: 'Contemporary',
        bedrooms: 4,
        bathrooms: 3,
        floors: 2,
        floorArea: 240,
        plotSizeDescription: '50×100 ft',
        hasGarage: true,
        estimatedCostMin: 8000000,
        estimatedCostMax: 12000000,
        filesIncludedText: 'Architectural drawings, Structural drawings, BOQ',
        fileFormats: ['PDF', 'DWG'],
        isFeatured: true,
        status: 'PUBLISHED',
        tags: ['maisonette', 'contemporary', 'garage'],
      },
    ],
  });

  console.log('Seed complete.');
}

main().finally(() => prisma.$disconnect());
```

---

### Phase 2 Deliverables Checklist

- [ ] `prisma/schema.prisma` with all 5 models defined and migrated
- [ ] `src/lib/prisma.ts` singleton
- [ ] `src/lib/validations.ts` with all Zod schemas
- [ ] `src/lib/cloudinary.ts` with image + file upload helpers
- [ ] `src/lib/email.ts` with inquiry notification emails
- [ ] `src/lib/auth.ts` NextAuth config + `middleware.ts`
- [ ] API routes: `GET/POST /api/designs`, `GET/PATCH/DELETE /api/designs/[id]`
- [ ] API routes: `GET/POST /api/inquiries`, `GET/PATCH /api/inquiries/[id]`
- [ ] `POST /api/upload` image + file upload route
- [ ] Seed script creates admin user and sample designs
- [ ] All routes tested via curl or Postman before moving to UI

---

## Phase 3: Public-Facing UI

> Goal: Every page a visitor can access — fully functional, responsive, and visually complete.

---

### 3.1 Global Layout

**`src/app/layout.tsx`**
- Load Playfair Display and Inter via `next/font/google`
- Wrap children in a `<Providers>` component (for future context providers)
- Include `<Navbar />` and `<Footer />` for all public routes

**`src/components/layout/Navbar.tsx`**
- Logo (text or SVG mark) on the left
- Navigation links: `Portfolio`, `About`, `Contact`
- CTA button on right: `Start an Inquiry` → links to `/inquire`
- Mobile: hamburger menu with a slide-down or drawer menu
- Sticky on scroll; transparent on hero, solid white/navy after scroll threshold

**`src/components/layout/Footer.tsx`**
- Logo + 1-line tagline
- Navigation columns: Pages, Contact
- Social links (LinkedIn, Instagram, WhatsApp)
- Copyright line
- Background: `brand.dark` with gold accent details

---

### 3.2 Home Page

Each section is a separate component mounted in `src/app/(public)/page.tsx`.

**`HeroSection.tsx`**
- Full-viewport height (`min-h-screen`)
- Background: high-quality image of the best design or a professional render — served via `next/image` with `priority` and `fill` props
- Dark overlay gradient for text contrast
- Headline in Playfair Display (large, `text-5xl lg:text-7xl`)
- Sub-headline in Inter (muted, `text-xl`)
- Two buttons: primary (filled gold) → `/portfolio`, secondary (outlined) → `/contact`
- Subtle scroll indicator arrow at bottom

**`FeaturedDesigns.tsx`**
- Fetch designs with `isFeatured: true` and `status: PUBLISHED` using a server component
- Display 3–6 `DesignCard` components in a responsive grid
- Section heading: "Featured Designs"
- "View All Designs →" link below grid

**`DesignCard.tsx`** (reused across portfolio and home):
```tsx
// Props:
interface DesignCardProps {
  design: {
    id: string;
    title: string;
    slug: string;
    style: string;
    bedrooms: number;
    bathrooms: number;
    floors: number;
    images: { url: string; label: string }[];
  };
}
```
- `next/image` for the thumbnail (aspect-ratio: 4/3, `object-cover`)
- Hover: subtle zoom on image + overlay with `View Design` button
- Below image: title, `style` badge, bedroom/bathroom/floor icons (Lucide icons)

**`ServicesSection.tsx`**
- Static content (hardcoded or from a config object — no DB needed)
- 3–4 service items: icon + title + 1-sentence description
- Grid layout with a light `brand.surface` background

**`HowItWorks.tsx`**
- 4 numbered steps in a horizontal timeline (desktop) / vertical (mobile)
- Step numbers styled in gold
- Each step: icon, title, 1-sentence description

**`TestimonialsSection.tsx`**
- Accepts an array of testimonial objects
- At launch: 2–3 real quotes if available; otherwise omit this section entirely (do not use fake quotes)
- Simple card layout with quote, name, and optional project reference

**`CTABanner.tsx`**
- Full-width band, `brand.primary` background, gold text accent
- Headline + `Start an Inquiry` button → `/inquire`

---

### 3.3 Portfolio Index Page

`src/app/(public)/portfolio/page.tsx` — Server Component with optional client-side filter state.

**Architecture:**
- Page is a Server Component that fetches the initial designs server-side (SSR)
- Filtering and search are handled client-side via URL search params (use `useSearchParams` + `router.push`) — this keeps filters shareable via URL

**`DesignFilters.tsx`**
- Style dropdown (dynamically populated from distinct `style` values in DB)
- Bedrooms select: 1, 2, 3, 4, 5+
- Floors select: 1, 2, 3+
- Search text input (debounced, 300ms)
- "Clear Filters" button
- On filter change → update URL params → Server Component re-fetches

**Design Grid:**
- Responsive: `grid-cols-1 md:grid-cols-2 lg:grid-cols-3`
- Each cell: `DesignCard` component
- Empty state: friendly message with CTA if no designs match filters

**Pagination:**
- Numbered pagination at the bottom (12 designs per page)
- Page numbers derived from total count ÷ limit

---

### 3.4 Design Detail Page

`src/app/(public)/portfolio/[slug]/page.tsx`

This is a **Server Component** that fetches the full design by slug. Use `generateStaticParams` for static generation of published designs at build time, with on-demand revalidation when the admin publishes a new design.

```ts
export async function generateStaticParams() {
  const designs = await prisma.design.findMany({
    where: { status: 'PUBLISHED' },
    select: { slug: true },
  });
  return designs.map((d) => ({ slug: d.slug }));
}

export const revalidate = 3600; // Revalidate at most every hour
```

**`DesignGallery.tsx`**
- Left/main: large primary image
- Right: vertical thumbnail strip (4–6 thumbnails)
- Click thumbnail → switch main image
- Click main image → open `yet-another-react-lightbox` with all images
- Each image has an accessible `alt` text from its `label` field
- Touch swipe support on mobile

**Specs Table:**
- Clean two-column table, alternating row background
- Data sourced directly from the design record
- Format numbers: `floorArea` → "240 m²", costs → "KES 8M – 12M"

**Rich Text Description:**
- Render `fullDescription` HTML using `dangerouslySetInnerHTML` — safe because it's admin-entered and sanitized on input
- Wrap in a `prose` class (Tailwind Typography plugin)

**"Files Included" Section:**
- Render `filesIncludedText` (rich text) in same prose container
- Below it: a row of `fileFormats` badges (e.g., `PDF`, `DWG`)

**"Request Access" Panel:**
- `position: sticky; top: 1.5rem` on desktop (sidebar)
- On mobile: full-width block below the description
- Gold border, slightly elevated card style
- Heading: "Interested in this design?"
- Body text: brief compelling copy
- Button: `Request Access` → `href="/inquire?design=[slug]&title=[title]"`

**Similar Designs:**
- Fetch up to 4 designs with the same `style`, excluding the current one
- Display as small `DesignCard` components in a horizontal scroll on mobile, 4-column grid on desktop

**Increment view count:**
- On page load, fire a `PATCH /api/designs/[id]/view` request client-side (fire-and-forget) to increment `viewCount`

---

### 3.5 Inquiry Page

`src/app/(public)/inquire/page.tsx`

**Pre-filling from URL params:**
```tsx
// On mount, read URL search params:
const searchParams = useSearchParams();
const designSlug = searchParams.get('design');
const designTitle = searchParams.get('title');
```

**`InquiryForm.tsx`** — Client Component using `react-hook-form` + Zod:

Form layout: two-column on desktop, single-column on mobile.

**Field implementation notes:**
- `designTitle` field: if pre-filled from URL, show as a read-only display pill with an "× Change" option that opens the dropdown
- `natureOfInterest`: rendered as 3 large radio cards with icons, not a dropdown — this is a key UX decision that feels less bureaucratic
- `country`: searchable select with a list of countries
- `budgetRange`: select options (e.g., "Under KES 5M", "KES 5M–10M", "KES 10M–20M", "Over KES 20M", "Not sure yet")
- `buildTimeline`: select options (e.g., "Within 3 months", "3–12 months", "1–2 years", "Just exploring")
- All required fields have visible asterisk markers
- Error messages appear inline below each field using `react-hook-form`'s `errors` state

**Submission:**
- `onSubmit` calls `POST /api/inquiries`
- Show a loading spinner on the button while submitting
- On success: replace form with a confirmation card:
  - Headline: "Your inquiry has been received!"
  - Body: "We'll review your request and get back to you at [email] within 24 hours."
  - CTA: "Browse More Designs →"
- On error: show an inline error banner

---

### 3.6 About Page

`src/app/(public)/about/page.tsx` — Static Server Component (no DB needed).

Content is hardcoded or sourced from a `content/about.ts` config file:

```ts
// src/content/about.ts
export const aboutContent = {
  name: 'John Doe',
  title: 'BSc. Civil & Structural Engineering',
  bio: `...`,
  credentials: [
    { label: 'EBK Registration', value: 'No. 12345' },
    { label: 'Member, IEK', value: '' },
  ],
  skills: ['Residential Design', 'Structural Analysis', '3D Visualization', ...],
  experience: '12+ years',
};
```

**Page layout:**
- Full-width hero with professional photo (if available) and name/title overlay
- Two-column bio section on desktop
- Credentials: displayed as a styled list with icons
- Skills: colorful tag badges in a flex-wrap container
- Bottom CTA: "View My Work →" and "Get In Touch →"

---

### 3.7 Contact Page

`src/app/(public)/contact/page.tsx`

- Simple contact form (name, email, subject, message)
- On submit → `POST /api/contact` → sends email to admin via Resend
- Below form: direct contact details (email, phone, WhatsApp link)
- WhatsApp link format: `https://wa.me/254XXXXXXXXX?text=Hello, I found your website...`
- Social media icons linking to profiles

---

### Phase 3 Deliverables Checklist

- [ ] Navbar with mobile hamburger and sticky scroll behavior
- [ ] Footer with all links and social icons
- [ ] Home page: all 7 sections complete
- [ ] `DesignCard` component (reusable across home and portfolio)
- [ ] Portfolio index: grid, filters, search, pagination — all functional
- [ ] Design detail page: gallery with lightbox, specs, rich text, sticky CTA panel, similar designs
- [ ] Inquiry page: pre-filling from URL, all fields, loading state, success/error states
- [ ] About page with content
- [ ] Contact page with form
- [ ] All pages are fully responsive on mobile, tablet, desktop
- [ ] All images use `next/image` with proper `alt` text

---

## Phase 4: Admin Panel

> Goal: A fully functional, intuitive admin dashboard for the engineer to manage designs and inquiries without technical assistance.

---

### 4.1 Admin Login Page

`src/app/(admin)/login/page.tsx`

- Centered card layout — clean, minimal
- Email + password fields
- "Sign In" button — shows loading state while NextAuth processes credentials
- Error message for invalid credentials: "Incorrect email or password."
- No "Forgot Password" link at V1 (admin resets manually via seed script)
- On success → redirect to `/admin`

---

### 4.2 Admin Layout

`src/app/(admin)/layout.tsx`

- Check session server-side; redirect to `/admin/login` if unauthenticated
- Render `<AdminSidebar />` + main content area
- On mobile: sidebar collapses to a top nav bar or slide-out drawer

**`AdminSidebar.tsx`:**
- Logo / site name at top
- Nav items with Lucide icons:
  - `LayoutDashboard` → `/admin`
  - `Images` → `/admin/designs`
  - `Mail` → `/admin/inquiries`
- New inquiry count badge on the Inquiries nav item (server-fetched count of `status: NEW`)
- Sign Out button at the bottom
- Active link highlighted with gold accent border-left

---

### 4.3 Admin Dashboard

`src/app/(admin)/admin/page.tsx` — Server Component

**Stat cards row (4 cards):**
- Total Designs Published
- Total Designs (Draft)
- Total Inquiries (All Time)
- New Inquiries (Unread)

Each stat card: large number, label, optional trend indicator, icon.

**Recent Inquiries table:**
- Last 5 inquiries
- Columns: Name, Design, Status badge, Date
- "View All Inquiries →" link

**Quick Actions:**
- `+ New Design` button → `/admin/designs/new`
- `View All Inquiries` button → `/admin/inquiries`

---

### 4.4 Design Management

#### Design List (`/admin/designs`)

Server Component — fetch all designs (both DRAFT and PUBLISHED).

**Table columns:**
- Thumbnail (40×40px)
- Title
- Style
- Beds / Baths
- Status (colored badge: green = Published, yellow = Draft)
- Inquiries (count)
- Featured (checkmark or dash)
- Created date
- Actions: `Edit` button, `Toggle Status` button (Publish/Unpublish), `Delete` button (with confirmation modal)

**Controls above table:**
- Search input (client-side filter on title)
- Status filter: All / Published / Draft
- `+ New Design` button (top right)

---

#### Design Form (`/admin/designs/new` and `/admin/designs/[id]/edit`)

This is the most complex component in the admin panel. Break it into tabbed sections to avoid an overwhelming single-scroll form.

**Tab 1: Basic Info**
- Title, Slug (auto-generated, editable)
- Short Description (`<textarea>`, character count shown)
- Style (text input with autocomplete suggestions from existing styles in DB)
- Bedrooms, Bathrooms, Floors (number inputs)
- Floor Area (m²), Plot Size Description
- Has Garage (toggle)
- Tags (tag input — type and press Enter to add)

**Tab 2: Description & Files**
- Full Description: Tiptap WYSIWYG rich text editor
  - Toolbar: Bold, Italic, Headings (H2/H3), Bullet List, Numbered List, Link
- Files Included Text: Tiptap editor (same toolbar)
- File Formats: tag input (e.g., "PDF", "DWG", "Revit")

**Tab 3: Images**
- `ImageUploader.tsx` component:
  - Drag-and-drop zone (dashed border, camera icon, "Drop images here or click to browse")
  - Accepts: `.jpg`, `.jpeg`, `.png`, `.webp` — max 10MB per file, max 20 images
  - On drop/select: upload immediately to Cloudinary via `POST /api/upload`
  - Show upload progress per file
  - After upload: display image grid with:
    - Preview thumbnail
    - Label input (editable inline)
    - "Set as Primary" radio button
    - Drag handle (reorder with `@dnd-kit/sortable`)
    - Delete button (with confirmation)

**Tab 4: Design Files (Secure)**
- `FileUploader.tsx` component:
  - Same drag-and-drop UX as images
  - Accepts: `.pdf`, `.dwg`, `.rvt`, `.zip`, `.dxf` — max 100MB per file
  - Upload to Cloudinary `authenticated` resource type
  - After upload: list view showing filename, type badge, file size
  - Delete button per file

**Tab 5: Settings**
- Status toggle: Draft / Published (large prominent toggle)
- Featured toggle: Yes / No
- Estimated Cost Min and Max (KES, number inputs)
- Preview button → opens `/portfolio/[slug]` in a new tab (only works if slug exists)

**Form Footer:**
- `Save as Draft` button (always available)
- `Save & Publish` button (sets status to PUBLISHED on save)
- `Discard Changes` link

**Save behavior:**
- On create: `POST /api/designs` with all data including image IDs and file IDs
- On edit: `PATCH /api/designs/[id]`
- Show a success toast notification on save
- Show inline field errors if validation fails

---

### 4.5 Inquiry Management

#### Inquiry List (`/admin/inquiries`)

Server Component with client-side status filter.

**Table columns:**
- Full Name
- Email (truncated)
- Design (linked to design detail page)
- Nature of Interest badge
- Country
- Submitted Date (relative: "2 hours ago")
- Status badge (color-coded: NEW=red, REVIEWED=yellow, RESPONDED=blue, CLOSED=gray)
- Actions: `View` button

**Filters above table:**
- Status filter: All / New / Reviewed / Responded / Closed
- Date range picker (optional for V1)
- Search by name or email

**Bulk action (optional V1):**
- Checkbox per row → "Mark selected as Reviewed" action

---

#### Inquiry Detail (`/admin/inquiries/[id]`)

Two-column layout on desktop.

**Left column — Client & Inquiry Details:**
- Client info card: name, email, phone, country (with copy buttons on email/phone)
- Inquiry details card: design linked, nature of interest, plot size, timeline, budget
- Message card: full message text
- "How did you hear about us": shown if filled

**Right column — Admin Actions:**
- Status select dropdown (NEW / REVIEWED / RESPONDED / CLOSED)
- "Update Status" button
- Admin Notes textarea (private — never shown to client)
- "Save Notes" button
- Reply via Email button:
  ```
  href="mailto:{inquiry.email}?subject=Re: Your Inquiry for {inquiry.designTitle}&body=Dear {inquiry.fullName},"
  ```
- Design preview link: "View Design →" opens design detail in new tab

**Navigation:**
- `← Back to Inquiries`
- `Previous Inquiry` / `Next Inquiry` (by date, filtered by current status filter)

---

### Phase 4 Deliverables Checklist

- [ ] Admin login page with credentials validation and error states
- [ ] Admin layout with sidebar, active link highlighting, and new inquiry badge
- [ ] Dashboard with 4 stat cards and recent inquiries table
- [ ] Design list with search, status filter, and action buttons
- [ ] Design create/edit form with 5 tabs — all fields functional
- [ ] Image uploader: drag-drop, Cloudinary upload, reorder, label, set primary, delete
- [ ] File uploader: drag-drop, Cloudinary secure upload, list view, delete
- [ ] Inquiry list with status filter and color-coded badges
- [ ] Inquiry detail with status update, admin notes, and mailto: reply link
- [ ] All admin pages redirect to login if unauthenticated

---

## Phase 5: Polish, SEO, Notifications & Launch Preparation

> Goal: Production-readiness. Every detail that makes the difference between a website and a professional product.

---

### 5.1 SEO Implementation

**`src/app/(public)/portfolio/[slug]/page.tsx` — Dynamic Metadata:**

```ts
export async function generateMetadata({ params }: { params: { slug: string } }) {
  const design = await prisma.design.findUnique({
    where: { slug: params.slug },
    include: { images: { where: { isPrimary: true }, take: 1 } },
  });

  if (!design) return {};

  const primaryImage = design.images[0]?.url;

  return {
    title: `${design.title} | ${design.bedrooms}-Bed ${design.style} House Plan`,
    description: design.shortDescription,
    openGraph: {
      title: design.title,
      description: design.shortDescription,
      images: primaryImage ? [{ url: primaryImage, width: 1200, height: 630 }] : [],
      type: 'website',
    },
    twitter: {
      card: 'summary_large_image',
      title: design.title,
      description: design.shortDescription,
      images: primaryImage ? [primaryImage] : [],
    },
  };
}
```

**Root layout metadata (`src/app/layout.tsx`):**
```ts
export const metadata = {
  title: {
    default: '[Engineer Name] | Structural & Architectural House Plans',
    template: '%s | [Engineer Name]',
  },
  description: 'Browse precision-engineered house plans for East Africa. Residential designs, structural drawings, and custom commissions.',
  metadataBase: new URL(process.env.NEXTAUTH_URL!),
};
```

**Sitemap (`src/app/sitemap.ts`):**
```ts
export default async function sitemap() {
  const designs = await prisma.design.findMany({
    where: { status: 'PUBLISHED' },
    select: { slug: true, updatedAt: true },
  });

  const designUrls = designs.map((d) => ({
    url: `${process.env.NEXTAUTH_URL}/portfolio/${d.slug}`,
    lastModified: d.updatedAt,
    changeFrequency: 'monthly' as const,
    priority: 0.8,
  }));

  return [
    { url: `${process.env.NEXTAUTH_URL}/`, priority: 1.0 },
    { url: `${process.env.NEXTAUTH_URL}/portfolio`, priority: 0.9 },
    { url: `${process.env.NEXTAUTH_URL}/about`, priority: 0.7 },
    { url: `${process.env.NEXTAUTH_URL}/contact`, priority: 0.6 },
    ...designUrls,
  ];
}
```

**`robots.txt` (`src/app/robots.ts`):**
```ts
export default function robots() {
  return {
    rules: [
      { userAgent: '*', allow: '/', disallow: ['/admin/', '/api/'] },
    ],
    sitemap: `${process.env.NEXTAUTH_URL}/sitemap.xml`,
  };
}
```

---

### 5.2 Performance Optimization

**Image optimization:**
- All `next/image` components use `sizes` prop correctly:
  ```tsx
  <Image
    src={design.primaryImageUrl}
    sizes="(max-width: 768px) 100vw, (max-width: 1200px) 50vw, 33vw"
    ...
  />
  ```
- Hero images use `priority` prop (no lazy loading for above-the-fold)
- Cloudinary transformation on upload auto-converts to WebP

**Portfolio grid performance:**
- Use `loading="lazy"` on all non-hero images
- Implement cursor-based pagination as an alternative to offset pagination when design count exceeds 100

**React Server Components:**
- All data-fetching pages are Server Components by default — do not add `'use client'` unless interactivity is required
- Client components only for: forms, filters, galleries, lightbox, drag-and-drop

**`next.config.ts` configuration:**
```ts
const nextConfig = {
  images: {
    remotePatterns: [
      { protocol: 'https', hostname: 'res.cloudinary.com' },
    ],
    formats: ['image/avif', 'image/webp'],
  },
  experimental: {
    optimizePackageImports: ['lucide-react'],
  },
};
```

---

### 5.3 Rate Limiting on Inquiry Form

Protect the `POST /api/inquiries` route from spam:

```ts
// src/lib/rateLimit.ts
import { LRUCache } from 'lru-cache';

const rateLimit = new LRUCache<string, number[]>({
  max: 500,
  ttl: 1000 * 60 * 15, // 15 minutes
});

export function checkRateLimit(ip: string, limit = 3): boolean {
  const now = Date.now();
  const windowMs = 15 * 60 * 1000;
  const timestamps = rateLimit.get(ip) ?? [];
  const recent = timestamps.filter((t) => now - t < windowMs);

  if (recent.length >= limit) return false;

  rateLimit.set(ip, [...recent, now]);
  return true;
}
```

Apply in the inquiry POST route:
```ts
const ip = req.headers.get('x-forwarded-for') ?? '127.0.0.1';
if (!checkRateLimit(ip)) {
  return NextResponse.json(
    { error: 'Too many requests. Please try again later.' },
    { status: 429 }
  );
}
```

---

### 5.4 Error Handling & Loading States

**Global Error Page (`src/app/error.tsx`):**
- Friendly error message with link back to home
- Log error details in development

**Not Found Page (`src/app/not-found.tsx`):**
- Branded 404 page: "This design doesn't exist — yet."
- CTA: "Browse All Designs"

**Loading states (`src/app/(public)/portfolio/loading.tsx`):**
- Skeleton loading cards matching the design card layout
- Use Tailwind's `animate-pulse` for skeleton shimmer

**Suspense boundaries:**
- Wrap the design grid in `<Suspense fallback={<DesignGridSkeleton />}>` for streaming SSR

---

### 5.5 Accessibility Audit

Before launch, verify:

- All interactive elements are keyboard accessible (Tab, Enter, Space, Escape)
- Color contrast ratio ≥ 4.5:1 for all text on background combinations
- All images have descriptive `alt` text (empty `alt=""` only for decorative images)
- Form fields have associated `<label>` elements (not just placeholder)
- Error messages are announced to screen readers via `aria-describedby`
- Lightbox traps focus while open, releases on close
- Modal has `role="dialog"` and `aria-modal="true"`

---

### 5.6 Environment & Deployment Configuration

**Production environment variables** (set in hosting platform — Vercel recommended):
```
DATABASE_URL              # Pooled connection (e.g., Supabase pgBouncer URL)
DIRECT_URL                # Direct connection for migrations
NEXTAUTH_SECRET           # Random 32+ char string: `openssl rand -base64 32`
NEXTAUTH_URL              # https://yourdomain.com
CLOUDINARY_CLOUD_NAME
CLOUDINARY_API_KEY
CLOUDINARY_API_SECRET
RESEND_API_KEY
ADMIN_EMAIL               # Engineer's email for notifications
```

**Pre-launch checklist:**
- [ ] Custom domain configured with HTTPS
- [ ] All environment variables set in production
- [ ] Database migrated to production (`npx prisma migrate deploy`)
- [ ] Admin account created in production via seed or Prisma Studio
- [ ] At least 10 real designs published with real images
- [ ] Test inquiry form end-to-end (submit → check admin email → check client email)
- [ ] Test admin login → create design → publish → verify on public site
- [ ] Run Lighthouse audit: target ≥ 90 on all four scores
- [ ] Check all OG images render when shared on WhatsApp

---

### 5.7 Post-Launch Monitoring

Set up these within 48 hours of launch:

- **Vercel Analytics** — enable in Vercel dashboard (zero-config, privacy-friendly)
- **Vercel Speed Insights** — core web vitals monitoring in production
- **Uptime monitoring** — free tier on UptimeRobot or Better Uptime (ping every 5 mins)

Optional for Month 2:
- Google Search Console — submit sitemap, monitor search appearance
- Google Analytics 4 — if the engineer wants detailed visitor behaviour

---

### Phase 5 Deliverables Checklist

- [ ] Dynamic `generateMetadata` on all public pages
- [ ] `sitemap.ts` and `robots.ts` implemented
- [ ] OG image tested on WhatsApp, Facebook, LinkedIn share preview
- [ ] Rate limiting on inquiry form
- [ ] Global error page, 404 page, loading skeletons
- [ ] Accessibility audit passed (keyboard nav, color contrast, ARIA labels)
- [ ] `next.config.ts` production-optimized
- [ ] All environment variables configured in production hosting
- [ ] Full end-to-end test on production (inquiry → admin email → admin panel)
- [ ] Lighthouse score ≥ 90 on Performance, Accessibility, Best Practices, SEO
- [ ] Uptime monitoring configured

---

## Summary Table

| Phase | What Gets Built | Estimated Days |
|---|---|---|
| **1 — Foundation** | Project setup, folder structure, design tokens, dependencies, ESLint/Prettier | 3–5 |
| **2 — Data Layer** | Prisma schema, API routes, Cloudinary, NextAuth, email, seed script | 3–4 |
| **3 — Public UI** | All 6 public pages: Home, Portfolio, Design Detail, Inquire, About, Contact | 6–9 |
| **4 — Admin Panel** | Login, Dashboard, Design CRUD + image/file uploaders, Inquiry management | 5–7 |
| **5 — Launch Prep** | SEO, performance, rate limiting, error pages, accessibility, deployment | 3–4 |
| **Total** | | **20–29 days** |

---

## Appendix A: Component Dependency Map

```
app/
├── (public)/
│   ├── page.tsx
│   │   ├── HeroSection
│   │   ├── FeaturedDesigns → DesignCard
│   │   ├── ServicesSection
│   │   ├── HowItWorks
│   │   ├── TestimonialsSection
│   │   └── CTABanner
│   ├── portfolio/page.tsx
│   │   ├── DesignFilters
│   │   └── DesignCard (×N)
│   ├── portfolio/[slug]/page.tsx
│   │   ├── DesignGallery → Lightbox
│   │   ├── SpecsTable
│   │   ├── RichTextRenderer
│   │   ├── AccessPanel → Button
│   │   └── SimilarDesigns → DesignCard (×4)
│   └── inquire/page.tsx
│       └── InquiryForm → Input, Select, Textarea, Button
├── (admin)/
│   ├── admin/page.tsx
│   │   └── StatCard (×4), RecentInquiriesTable
│   ├── admin/designs/
│   │   ├── page.tsx → DesignTable
│   │   └── new/page.tsx → DesignForm
│   │       ├── Tab: BasicInfo → Input, Select, TagInput
│   │       ├── Tab: Description → TiptapEditor (×2)
│   │       ├── Tab: Images → ImageUploader → DndKit
│   │       ├── Tab: Files → FileUploader
│   │       └── Tab: Settings → Toggle, Input
│   └── admin/inquiries/
│       ├── page.tsx → InquiryTable
│       └── [id]/page.tsx → InquiryDetail
```

---

## Appendix B: Key Technical Decisions

| Decision | Choice | Rationale |
|---|---|---|
| Routing strategy | App Router (Server Components first) | Better performance, simpler data fetching patterns for a mostly-read public site |
| Auth library | NextAuth v5 (beta) | Native App Router support, minimal config for single-admin credentials auth |
| Image storage | Cloudinary | Built-in transformation (WebP conversion, resizing), CDN delivery, generous free tier |
| Design files storage | Cloudinary `authenticated` type | Same provider as images; `authenticated` delivery type ensures files are never publicly accessible without a signed URL |
| Email service | Resend | Excellent Next.js integration, reliable deliverability, React Email template support |
| Rich text | Tiptap | Headless, fully customizable, React-native; no legacy jQuery dependency like Quill |
| Form management | react-hook-form + Zod | Best-in-class performance (uncontrolled inputs), type-safe validation, great error UX |
| CSS approach | Tailwind CSS | Fastest to iterate; design tokens in `tailwind.config.ts` ensure consistency |
| Drag-and-drop | @dnd-kit | Accessible, React-native, actively maintained; better than react-beautiful-dnd for Next.js |

---

*End of Implementation Plan*