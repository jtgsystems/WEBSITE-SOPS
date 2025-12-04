# Webflow Development - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2025
**Source:** [developers.webflow.com](https://developers.webflow.com)

---

## Overview

Webflow is a visual web development platform that combines design, CMS, and hosting. This SOP covers best practices for professional Webflow development, including project setup, design standards, CMS configuration, and deployment workflows.

---

## Project Setup

### Initial Configuration

```markdown
New Project Checklist:
[ ] Create project with appropriate plan (Starter/CMS/Business/Enterprise)
[ ] Configure project settings (name, subdomain, favicon)
[ ] Set up custom domain (if applicable)
[ ] Enable SSL certificate
[ ] Configure 301 redirects for existing site
[ ] Set default SEO settings
[ ] Enable site-wide backup
```

### Project Structure

```
Project Organization:
├── Pages
│   ├── Home
│   ├── About
│   ├── Services
│   ├── Contact
│   └── Utility Pages
│       ├── 404
│       ├── Password
│       └── Search Results
├── CMS Collections
│   ├── Blog Posts
│   ├── Team Members
│   ├── Projects/Case Studies
│   └── Testimonials
├── Symbols (Components)
│   ├── Navigation
│   ├── Footer
│   ├── CTAs
│   └── Cards
└── Assets
    ├── Images
    ├── Videos
    └── Documents
```

### Style Guide Setup

```css
/* Create Global CSS Variables */
:root {
  /* Colors */
  --color-primary: #0066FF;
  --color-primary-dark: #0052CC;
  --color-secondary: #1A1A2E;
  --color-accent: #00D4AA;
  --color-text: #333333;
  --color-text-light: #666666;
  --color-background: #FFFFFF;
  --color-background-alt: #F5F5F5;

  /* Typography */
  --font-primary: 'Inter', sans-serif;
  --font-secondary: 'Playfair Display', serif;

  /* Spacing */
  --space-xs: 0.5rem;
  --space-sm: 1rem;
  --space-md: 2rem;
  --space-lg: 4rem;
  --space-xl: 8rem;

  /* Transitions */
  --transition-fast: 150ms ease;
  --transition-medium: 300ms ease;
  --transition-slow: 500ms ease;
}
```

---

## Design Standards

### Responsive Breakpoints

| Breakpoint | Width | Common Devices |
|------------|-------|----------------|
| Desktop (Base) | 1920px+ | Large monitors |
| Desktop | 1440px | Standard desktop |
| Laptop | 1280px | Laptops |
| Tablet Landscape | 991px | iPad landscape |
| Tablet Portrait | 767px | iPad portrait |
| Mobile Landscape | 478px | Phone landscape |
| Mobile Portrait | 320px | Phone portrait |

### Class Naming Convention

```markdown
BEM-Inspired Naming:
block_element-modifier

Examples:
- hero_heading-large
- nav_link-active
- card_image-rounded
- section_wrapper-dark
- button_primary-large

Utility Classes:
- hide-mobile
- show-tablet
- text-center
- margin-top-lg
```

### Typography Scale

```markdown
Desktop Typography:
- H1: 72px / 1.1 line-height
- H2: 48px / 1.2 line-height
- H3: 32px / 1.3 line-height
- H4: 24px / 1.4 line-height
- Body: 18px / 1.6 line-height
- Small: 14px / 1.5 line-height

Mobile Typography (scale down ~20%):
- H1: 48px
- H2: 36px
- H3: 28px
- Body: 16px
```

---

## CMS Configuration

### Collection Structure

```javascript
// Blog Posts Collection
{
  name: "Blog Posts",
  slug: "blog",
  fields: [
    { name: "Title", type: "PlainText", required: true },
    { name: "Slug", type: "PlainText", required: true },
    { name: "Featured Image", type: "ImageRef" },
    { name: "Excerpt", type: "PlainText", maxLength: 200 },
    { name: "Content", type: "RichText" },
    { name: "Author", type: "Reference", collection: "Team" },
    { name: "Categories", type: "MultiReference", collection: "Categories" },
    { name: "Published Date", type: "DateTime" },
    { name: "Featured", type: "Switch" },
    { name: "SEO Title", type: "PlainText" },
    { name: "SEO Description", type: "PlainText" }
  ]
}
```

### Dynamic Pages

```markdown
Template Page Setup:
1. Create Collection Template page
2. Bind dynamic content to elements
3. Set up filtering and sorting
4. Configure pagination
5. Add related content sections
6. Set up SEO meta fields

URL Structure:
/blog                      → Collection list page
/blog/[slug]               → Individual post page
/blog/category/[category]  → Category archive
```

---

## Webflow API Integration

### Apps & Integrations

```javascript
// Webflow API Configuration
const webflowConfig = {
  apiVersion: 'v2',
  baseUrl: 'https://api.webflow.com/v2',
  siteId: process.env.WEBFLOW_SITE_ID,
  collectionId: process.env.WEBFLOW_COLLECTION_ID
};

// Fetch CMS Items
async function getCMSItems(collectionId) {
  const response = await fetch(
    `${webflowConfig.baseUrl}/collections/${collectionId}/items`,
    {
      headers: {
        'Authorization': `Bearer ${process.env.WEBFLOW_API_TOKEN}`,
        'accept-version': '2.0.0'
      }
    }
  );
  return response.json();
}

// Create CMS Item
async function createCMSItem(collectionId, data) {
  const response = await fetch(
    `${webflowConfig.baseUrl}/collections/${collectionId}/items`,
    {
      method: 'POST',
      headers: {
        'Authorization': `Bearer ${process.env.WEBFLOW_API_TOKEN}`,
        'Content-Type': 'application/json',
        'accept-version': '2.0.0'
      },
      body: JSON.stringify({
        isArchived: false,
        isDraft: false,
        fieldData: data
      })
    }
  );
  return response.json();
}
```

### Form Handling

```javascript
// Custom Form Submission Handler
document.addEventListener('DOMContentLoaded', function() {
  const form = document.querySelector('[data-form="contact"]');

  form.addEventListener('submit', async function(e) {
    e.preventDefault();

    const formData = new FormData(form);
    const data = Object.fromEntries(formData);

    try {
      // Send to custom endpoint
      const response = await fetch('/api/contact', {
        method: 'POST',
        headers: { 'Content-Type': 'application/json' },
        body: JSON.stringify(data)
      });

      if (response.ok) {
        // Show success message
        form.querySelector('.w-form-done').style.display = 'block';
        form.querySelector('.w-form-fail').style.display = 'none';
      } else {
        throw new Error('Submission failed');
      }
    } catch (error) {
      form.querySelector('.w-form-fail').style.display = 'block';
    }
  });
});
```

---

## Performance Optimization

### Image Optimization

```markdown
Image Guidelines:
- Use WebP format (auto-converted by Webflow)
- Set appropriate sizes for each breakpoint
- Enable lazy loading for below-fold images
- Use responsive images (srcset)
- Compress images before upload (target: <200KB)
- Use CDN (automatically enabled)

Recommended Tools:
- TinyPNG/TinyJPG for compression
- Squoosh for format conversion
- ImageOptim for batch processing
```

### Custom Code Performance

```html
<!-- Load scripts with defer -->
<script src="custom.js" defer></script>

<!-- Preconnect to external resources -->
<link rel="preconnect" href="https://fonts.googleapis.com">
<link rel="preconnect" href="https://www.google-analytics.com">

<!-- Lazy load iframes -->
<iframe src="video.html" loading="lazy"></iframe>
```

### Webflow-Specific Optimizations

```markdown
Performance Checklist:
[ ] Minimize custom code usage
[ ] Use native Webflow interactions (not JS libraries)
[ ] Optimize symbol usage (reuse components)
[ ] Clean up unused styles and classes
[ ] Remove unused pages and CMS items
[ ] Enable Webflow's image optimization
[ ] Minimize third-party embeds
[ ] Use system fonts when possible
```

---

## SEO Best Practices

### On-Page SEO

```markdown
Page-Level Settings:
- Unique title tag (50-60 characters)
- Meta description (150-160 characters)
- Canonical URL
- Open Graph image (1200x630px)
- Twitter card settings
- Custom 301 redirects

Technical SEO:
- Clean URL structure
- XML sitemap (auto-generated)
- Robots.txt configuration
- Schema markup (via custom code)
- Alt text for all images
- Proper heading hierarchy (H1 → H6)
```

### Schema Markup

```html
<!-- Organization Schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "Organization",
  "name": "Your Company",
  "url": "https://yoursite.com",
  "logo": "https://yoursite.com/logo.png",
  "sameAs": [
    "https://twitter.com/yourcompany",
    "https://linkedin.com/company/yourcompany"
  ]
}
</script>

<!-- Blog Post Schema -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "BlogPosting",
  "headline": "{{wf {\"path\":\"name\",\"type\":\"PlainText\"} }}",
  "image": "{{wf {\"path\":\"featured-image\",\"type\":\"ImageRef\"} }}",
  "datePublished": "{{wf {\"path\":\"published-date\",\"type\":\"DateTime\"} }}",
  "author": {
    "@type": "Person",
    "name": "{{wf {\"path\":\"author\",\"type\":\"Reference\"} }}"
  }
}
</script>
```

---

## Collaboration & Version Control

### Team Workflow

```markdown
Development Process:
1. Designer creates in Staging environment
2. Developer reviews and optimizes
3. QA tests across devices
4. Client reviews and provides feedback
5. Revisions implemented
6. Final QA check
7. Publish to Production

Best Practices:
- Use page-level notes for handoff
- Create a style guide page
- Document custom code
- Maintain component library (Symbols)
- Regular backups before major changes
```

### Backup & Restore

```markdown
Backup Strategy:
- Enable automatic backups (Webflow feature)
- Create manual backup before major changes
- Export site code periodically (for archival)
- Document CMS structure separately

Restore Process:
1. Access Site Settings → Backups
2. Select backup point to restore
3. Confirm restoration
4. Verify all pages and functionality
5. Re-publish if needed
```

---

## Deployment

### Pre-Launch Checklist

```markdown
Content:
[ ] All placeholder content replaced
[ ] Links tested (internal and external)
[ ] Forms tested and connected
[ ] Legal pages in place (Privacy, Terms)
[ ] 404 page customized
[ ] Favicon uploaded

Technical:
[ ] SSL enabled
[ ] Custom domain connected
[ ] Redirects configured
[ ] Analytics installed
[ ] Search engine indexing enabled
[ ] Sitemap submitted to Google

Performance:
[ ] Lighthouse score > 90
[ ] Mobile responsiveness verified
[ ] Load time < 3 seconds
[ ] Images optimized
```

### Publishing Process

```markdown
Staging → Production:
1. Final review on staging URL
2. Client approval obtained
3. Backup current production
4. Publish changes
5. Clear CDN cache (if needed)
6. Verify live site
7. Monitor for issues
```

---

## Common Integrations

### Third-Party Tools

| Category | Tool | Integration Method |
|----------|------|-------------------|
| Analytics | Google Analytics 4 | Custom code |
| Forms | Zapier/Make | Webhook |
| E-commerce | Shopify | Buy buttons/embeds |
| Email | Mailchimp/ConvertKit | Embed/API |
| Chat | Intercom/Drift | Custom code |
| Scheduling | Calendly | Embed |
| Video | Vimeo/Wistia | Embed |

---

## Resources

- [Webflow University](https://university.webflow.com/)
- [Webflow API Docs](https://developers.webflow.com/)
- [Webflow Forums](https://forum.webflow.com/)
- [Finsweet Client-First](https://finsweet.com/client-first)

---

**Document Version:** 1.0
**Maintained by:** Web Development Team
**Review Cycle:** Quarterly
