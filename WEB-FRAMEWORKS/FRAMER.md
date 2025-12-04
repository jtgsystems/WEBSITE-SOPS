# Framer Development - Standard Operating Procedure

**Version:** 1.0
**Last Updated:** December 2025
**Source:** [framer.com/developers](https://www.framer.com/developers/)

---

## Overview

Framer is a powerful design and development platform that enables creating high-performance websites with advanced animations, interactions, and React components. This SOP covers professional Framer development workflows, component creation, and deployment best practices.

---

## Project Setup

### Initial Configuration

```markdown
New Project Checklist:
[ ] Create new project (blank or from template)
[ ] Configure project settings
[ ] Set up custom domain
[ ] Enable analytics
[ ] Configure SEO defaults
[ ] Set up version history
[ ] Connect to Git (optional)
```

### Project Structure

```
Framer Project Organization:
├── Pages
│   ├── Home
│   ├── About
│   ├── Work
│   ├── Contact
│   └── _404 (error page)
├── Components
│   ├── Layout
│   │   ├── Header
│   │   ├── Footer
│   │   └── Section
│   ├── UI
│   │   ├── Button
│   │   ├── Card
│   │   └── Modal
│   └── Custom
│       └── [React Components]
├── CMS Collections
│   ├── Projects
│   ├── Blog Posts
│   └── Team Members
└── Assets
    ├── Images
    ├── Icons
    └── Videos
```

---

## Design System Setup

### Design Tokens

```typescript
// Design tokens configuration
const designTokens = {
  colors: {
    primary: {
      50: '#E6F0FF',
      100: '#CCE0FF',
      500: '#0066FF',
      600: '#0052CC',
      900: '#001A4D'
    },
    neutral: {
      0: '#FFFFFF',
      50: '#F5F5F5',
      100: '#E0E0E0',
      500: '#666666',
      900: '#1A1A1A'
    }
  },
  typography: {
    fontFamily: {
      primary: '"Inter", sans-serif',
      secondary: '"Playfair Display", serif',
      mono: '"JetBrains Mono", monospace'
    },
    fontSize: {
      xs: '12px',
      sm: '14px',
      base: '16px',
      lg: '18px',
      xl: '24px',
      '2xl': '32px',
      '3xl': '48px',
      '4xl': '64px'
    }
  },
  spacing: {
    xs: '4px',
    sm: '8px',
    md: '16px',
    lg: '32px',
    xl: '64px',
    '2xl': '128px'
  },
  breakpoints: {
    mobile: '390px',
    tablet: '810px',
    desktop: '1200px',
    wide: '1728px'
  }
};
```

### Responsive Design

```markdown
Breakpoint Strategy:
- Desktop (1200px+): Full layout, all features
- Tablet (810px - 1199px): Adjusted layout, condensed navigation
- Mobile (< 810px): Stacked layout, hamburger menu

Best Practices:
- Design mobile-first when possible
- Use Framer's responsive variants
- Test on actual devices
- Use fluid typography and spacing
```

---

## Custom Components

### React Component Structure

```tsx
// components/CustomButton.tsx
import { motion } from "framer-motion"
import { addPropertyControls, ControlType } from "framer"

interface Props {
  text: string
  variant: "primary" | "secondary" | "outline"
  size: "small" | "medium" | "large"
  onClick?: () => void
}

export function CustomButton({
  text = "Click me",
  variant = "primary",
  size = "medium",
  onClick
}: Props) {
  const variants = {
    primary: {
      background: "#0066FF",
      color: "#FFFFFF"
    },
    secondary: {
      background: "#1A1A2E",
      color: "#FFFFFF"
    },
    outline: {
      background: "transparent",
      color: "#0066FF",
      border: "2px solid #0066FF"
    }
  }

  const sizes = {
    small: { padding: "8px 16px", fontSize: "14px" },
    medium: { padding: "12px 24px", fontSize: "16px" },
    large: { padding: "16px 32px", fontSize: "18px" }
  }

  return (
    <motion.button
      style={{
        ...variants[variant],
        ...sizes[size],
        borderRadius: "8px",
        fontWeight: 600,
        cursor: "pointer",
        border: variant === "outline" ? variants.outline.border : "none"
      }}
      whileHover={{ scale: 1.02 }}
      whileTap={{ scale: 0.98 }}
      onClick={onClick}
    >
      {text}
    </motion.button>
  )
}

// Property controls for the Framer UI
addPropertyControls(CustomButton, {
  text: {
    type: ControlType.String,
    title: "Text",
    defaultValue: "Click me"
  },
  variant: {
    type: ControlType.Enum,
    title: "Variant",
    options: ["primary", "secondary", "outline"],
    defaultValue: "primary"
  },
  size: {
    type: ControlType.Enum,
    title: "Size",
    options: ["small", "medium", "large"],
    defaultValue: "medium"
  }
})
```

### Data Fetching Component

```tsx
// components/BlogList.tsx
import { useEffect, useState } from "react"
import { addPropertyControls, ControlType } from "framer"

interface BlogPost {
  id: string
  title: string
  excerpt: string
  image: string
  slug: string
}

interface Props {
  apiUrl: string
  limit: number
}

export function BlogList({ apiUrl, limit = 6 }: Props) {
  const [posts, setPosts] = useState<BlogPost[]>([])
  const [loading, setLoading] = useState(true)

  useEffect(() => {
    async function fetchPosts() {
      try {
        const response = await fetch(`${apiUrl}?limit=${limit}`)
        const data = await response.json()
        setPosts(data.posts)
      } catch (error) {
        console.error('Failed to fetch posts:', error)
      } finally {
        setLoading(false)
      }
    }
    fetchPosts()
  }, [apiUrl, limit])

  if (loading) {
    return <div>Loading...</div>
  }

  return (
    <div style={{
      display: "grid",
      gridTemplateColumns: "repeat(auto-fill, minmax(300px, 1fr))",
      gap: "24px"
    }}>
      {posts.map(post => (
        <article key={post.id} style={{
          borderRadius: "12px",
          overflow: "hidden",
          background: "#fff",
          boxShadow: "0 4px 20px rgba(0,0,0,0.1)"
        }}>
          <img
            src={post.image}
            alt={post.title}
            style={{ width: "100%", height: "200px", objectFit: "cover" }}
          />
          <div style={{ padding: "20px" }}>
            <h3 style={{ margin: 0, fontSize: "20px" }}>{post.title}</h3>
            <p style={{ color: "#666", marginTop: "8px" }}>{post.excerpt}</p>
          </div>
        </article>
      ))}
    </div>
  )
}

addPropertyControls(BlogList, {
  apiUrl: {
    type: ControlType.String,
    title: "API URL",
    defaultValue: "https://api.example.com/posts"
  },
  limit: {
    type: ControlType.Number,
    title: "Limit",
    defaultValue: 6,
    min: 1,
    max: 20
  }
})
```

---

## Animations & Interactions

### Page Transitions

```tsx
// Smooth page transitions
const pageTransition = {
  initial: { opacity: 0, y: 20 },
  animate: { opacity: 1, y: 0 },
  exit: { opacity: 0, y: -20 },
  transition: { duration: 0.4, ease: [0.25, 0.1, 0.25, 1] }
}
```

### Scroll Animations

```tsx
// Scroll-triggered animations
import { motion, useScroll, useTransform } from "framer-motion"

function ParallaxSection() {
  const { scrollYProgress } = useScroll()
  const y = useTransform(scrollYProgress, [0, 1], [0, -100])

  return (
    <motion.div style={{ y }}>
      <h2>Parallax Content</h2>
    </motion.div>
  )
}

// Staggered children animation
const container = {
  hidden: { opacity: 0 },
  show: {
    opacity: 1,
    transition: {
      staggerChildren: 0.1
    }
  }
}

const item = {
  hidden: { opacity: 0, y: 20 },
  show: { opacity: 1, y: 0 }
}

function StaggeredList({ items }) {
  return (
    <motion.ul variants={container} initial="hidden" animate="show">
      {items.map((item, i) => (
        <motion.li key={i} variants={item}>
          {item}
        </motion.li>
      ))}
    </motion.ul>
  )
}
```

### Interaction Best Practices

```markdown
Performance Guidelines:
- Use transform and opacity for animations (GPU accelerated)
- Avoid animating layout properties (width, height, padding)
- Use will-change sparingly
- Keep animations under 300ms for UI feedback
- Use reduced-motion media query for accessibility

Accessibility:
- Respect prefers-reduced-motion
- Provide keyboard alternatives for hover states
- Ensure focus states are visible
- Don't rely solely on animation for information
```

---

## CMS Integration

### Collection Setup

```typescript
// CMS Collection Schema Example
const projectsCollection = {
  name: "Projects",
  slug: "projects",
  fields: [
    { name: "title", type: "text", required: true },
    { name: "slug", type: "slug", required: true },
    { name: "description", type: "richtext" },
    { name: "thumbnail", type: "image" },
    { name: "images", type: "gallery" },
    { name: "category", type: "reference", collection: "categories" },
    { name: "client", type: "text" },
    { name: "year", type: "number" },
    { name: "featured", type: "boolean" },
    { name: "liveUrl", type: "url" }
  ]
}
```

### Dynamic Pages

```markdown
CMS Page Setup:
1. Create CMS Collection (e.g., "Projects")
2. Add fields matching content needs
3. Create Collection page template
4. Bind CMS data to components
5. Configure slug for URL structure
6. Set up filtering/sorting as needed
7. Add pagination for lists
```

---

## Performance Optimization

### Image Optimization

```markdown
Image Best Practices:
- Use Framer's automatic image optimization
- Set appropriate sizes for each breakpoint
- Use lazy loading for below-fold images
- Prefer WebP/AVIF formats
- Compress images before upload
- Use appropriate aspect ratios
```

### Code Optimization

```typescript
// Lazy load heavy components
import { lazy, Suspense } from "react"

const HeavyComponent = lazy(() => import("./HeavyComponent"))

function Page() {
  return (
    <Suspense fallback={<div>Loading...</div>}>
      <HeavyComponent />
    </Suspense>
  )
}

// Memoize expensive computations
import { useMemo } from "react"

function DataList({ items }) {
  const processedItems = useMemo(() => {
    return items.map(item => ({
      ...item,
      computed: expensiveComputation(item)
    }))
  }, [items])

  return <List items={processedItems} />
}
```

---

## SEO Configuration

### Page SEO Settings

```markdown
Per-Page SEO:
- Title tag (50-60 characters)
- Meta description (150-160 characters)
- Open Graph image (1200x630px)
- Canonical URL
- Robots directives

Site-Wide SEO:
- Sitemap (auto-generated)
- Robots.txt
- 301 redirects
- Structured data (JSON-LD)
```

### Structured Data

```html
<!-- Add to page custom code -->
<script type="application/ld+json">
{
  "@context": "https://schema.org",
  "@type": "WebSite",
  "name": "Your Site Name",
  "url": "https://yoursite.com"
}
</script>
```

---

## Deployment

### Publishing Workflow

```markdown
Pre-Publish Checklist:
[ ] All content finalized
[ ] Responsive design tested
[ ] Forms connected and tested
[ ] Analytics configured
[ ] SEO settings complete
[ ] Performance optimized
[ ] Accessibility verified
[ ] Custom domain configured
[ ] SSL enabled

Publishing Steps:
1. Preview and test thoroughly
2. Client approval (if applicable)
3. Publish to staging (optional)
4. Publish to production
5. Verify live site
6. Submit sitemap to search engines
```

### Custom Domain Setup

```markdown
Domain Configuration:
1. Add custom domain in Framer settings
2. Update DNS records:
   - A record: Point to Framer IP
   - CNAME: www → your-project.framer.website
3. Wait for DNS propagation (up to 48 hours)
4. SSL certificate auto-provisions
5. Verify HTTPS is working
```

---

## Version Control & Collaboration

### Team Workflow

```markdown
Collaboration Best Practices:
- Use version history for checkpoints
- Comment on specific elements
- Share preview links for feedback
- Use branching for major changes
- Document component usage
- Maintain a style guide page
```

### Git Integration

```markdown
For code components:
- Use external code editor
- Version control component code
- Import components via packages
- Keep dependencies updated
- Document component APIs
```

---

## Resources

- [Framer Learn](https://www.framer.com/learn/)
- [Framer Developers](https://www.framer.com/developers/)
- [Framer Motion](https://www.framer.com/motion/)
- [Framer Community](https://www.framer.com/community/)

---

**Document Version:** 1.0
**Maintained by:** Design & Development Team
**Review Cycle:** Quarterly
