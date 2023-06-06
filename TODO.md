Run Paul's repo
- swap out index page for blog page
- swap out index layout page for blog layout page

- Components
    - PageHeader
    - fetchAPI
    - BlogList
    - ArticleSelect
    - Post
    - 


Create Loader component


## Dependencies to add

- @types/negotiator
- negotiator
- @types/qs
- qs
- classnames
- react-icons
- react-markdown
- react-slideshow-image
- remark-gfm

## DevDependencies to add

- autoprefixer
- postcss
- tailwindcss



# Content-Type Builder

## Collection Types
- Article
    - title: Text (Short text)
    - description: Text (Long text)
    - slug: UID (title)
    - cover: Media (Single media)/(Files, Images, Videos)
    - category: Relation with Category (Category has many Articles)
    - blocks: Dynamic zone
        - Media
            - file: Media (Single media)/(Images)
        - Quote
            - title: Text (Short text)
            - body: Text (Long text)
            - author: Text (Short text)
        - Rich text
            - body: Rich text
        - Slider
            - files: Media (Multiple media)/(Images)
        - Video Embed
            - url: Text (Short text)
    - authorsBio: Relation with Author (Author has many Articles)
    - seo: Component
        - metaTitle: Text (Short text)
        - metaDescription: Text (Long text)
        - shareImage: Media (Single media)/(Images)
- Author
    - name: Text (Short text)
    - avatar: Media (Single media)/(Files, Images, Videos)
    - email: Text (Short text)
    - articles: Relation with Article (Author belongs to many Articles)
- Category
    - name: Text (Short text)
    - slug: UID 
    - articles: Relation with Article (Category belongs to many Articles)
    - description: Text (Long text)
- Lead form submission
    - email: Text (Short text)
    - status: Enumeration (seen, contacted, ignored)
- Page
    - shortName: Text (Short text)
    - metaData: Component
        - metaTitle: Text (Short text)
        - metaDescription: Text (Long text)
    - contentSections: Dynamic Zone
        - Hero
            - title: Text (Short text)
            - description: Text (Long text)
            - picture: Media (Single media)/(Images)
            - buttons: Component (repeatable)
                - url: Text (Short text)
                - newTab: Boolean (false)
                - text: Text (Short text)
                - type: Enumeration (primary, secondary)
    - slug: Text (Short text)/(^$|^[a-zA-Z/-]+$)
    - heading: Text (Short text)
    - description: Text (Long text)
- Product Feature
    - name: Text (Short text)
- User


## Single Types

- Global
    - metaData: Component
        - metaTitle: Text (Short text)
        - metaDescription: Text (Long text)
    - favicon: Media (Single media)/(Images)
    - notificationBanner: Component
        - type: Enumeration (alert, info, warning)
        - heading: Text (Short text)
        - text: Text (Long text)

## Components

- Elements
    - Feature
    - Feature column
    - Feature row
    - Footer section
    - Logos
    - Notification banner
    - Pricing plan
    - Testimonial
- Layout
    - Footer
    - Logo
    - Navbar
- Links
    - Button
    - Button link
    - Link
    - Social Link
- Meta
    - Metadata
- Sections
    - Bottom actions
    - Feature columns group
    - Heading
    - Hero
    - Large video
    - Lead form
    - Pricing
    - Rich text
    - Testimonials group
- Shared
    - Media
    - Quote
    - Rich text
    - Seo
    - Slider
    - Video embed


