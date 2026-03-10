# Design Document: Bali Tourism Website

## Overview

The Bali Tourism Website is a client-side web application built with HTML5, CSS (Tailwind), and Vanilla JavaScript. The system provides a responsive, accessible platform for international tourists to discover and explore Bali attractions through search, filtering, detailed destination pages, and user-generated reviews.

The architecture follows a component-based approach with clear separation between data management, UI rendering, and user interaction handling. The application uses local storage for data persistence and implements a simple routing system for navigation between the homepage and destination detail pages.

### Key Design Principles

- **Client-side architecture**: All logic runs in the browser with no backend server
- **Component-based UI**: Reusable UI components for consistency and maintainability
- **Responsive-first design**: Mobile-first approach with progressive enhancement
- **Accessibility compliance**: WCAG 2.1 AA standards for inclusive user experience
- **Performance optimization**: Lazy loading, image optimization, and efficient DOM manipulation

## Architecture

### System Architecture

The application follows a layered architecture pattern:

```
┌─────────────────────────────────────────────────────────┐
│                    Presentation Layer                    │
│  (UI Components, Event Handlers, DOM Manipulation)      │
└─────────────────────────────────────────────────────────┘
                          │
┌─────────────────────────────────────────────────────────┐
│                   Application Layer                      │
│     (Business Logic, State Management, Routing)         │
└─────────────────────────────────────────────────────────┘
                          │
┌─────────────────────────────────────────────────────────┐
│                      Data Layer                          │
│        (Data Models, Storage, Data Access)              │
└─────────────────────────────────────────────────────────┘
```

### Directory Structure

```
bali-tourism-website/
├── index.html                 # Homepage
├── destination.html           # Destination detail page template
├── css/
│   └── styles.css            # Custom styles (Tailwind + custom)
├── js/
│   ├── main.js               # Application initialization
│   ├── data/
│   │   ├── destinations.js   # Destination data store
│   │   └── storage.js        # Local storage management
│   ├── components/
│   │   ├── destinationCard.js
│   │   ├── searchBar.js
│   │   ├── filterSystem.js
│   │   ├── gallery.js
│   │   ├── ratingWidget.js
│   │   ├── reviewForm.js
│   │   └── mapComponent.js
│   ├── pages/
│   │   ├── homepage.js       # Homepage logic
│   │   └── detailPage.js     # Detail page logic
│   └── utils/
│       ├── router.js         # Client-side routing
│       ├── validators.js     # Input validation
│       └── helpers.js        # Utility functions
├── images/
│   ├── destinations/         # Destination images
│   └── placeholders/         # Fallback images
└── data/
    └── initial-data.json     # Seed data for destinations
```

### Technology Stack

- **HTML5**: Semantic markup structure
- **Tailwind CSS**: Utility-first CSS framework for responsive design
- **Vanilla JavaScript (ES6+)**: No framework dependencies
- **Local Storage API**: Client-side data persistence
- **Google Maps Embed API**: Map integration (or OpenStreetMap alternative)
- **Intersection Observer API**: Lazy loading implementation

## Components and Interfaces

### Core Components

#### 1. DestinationCard Component

Displays summary information for a destination in a card format.

**Interface:**
```javascript
class DestinationCard {
  constructor(destination) {
    this.destination = destination;
  }
  
  render() {
    // Returns HTML string or DOM element
  }
  
  attachEventListeners() {
    // Handles click events for navigation
  }
}
```

**Props:**
- `id`: string - Unique destination identifier
- `name`: string - Destination name
- `category`: string - Category type
- `shortDescription`: string - Brief description (max 150 chars)
- `location`: string - Location name
- `primaryImage`: string - Image URL
- `averageRating`: number - Calculated average (0-5)
- `reviewCount`: number - Total reviews

**Rendering:**
- Image with lazy loading
- Name as heading
- Short description
- Location with icon
- Rating widget (stars + count)
- Click handler for navigation

#### 2. SearchBar Component

Provides text input for searching destinations.

**Interface:**
```javascript
class SearchBar {
  constructor(onSearch) {
    this.onSearch = onSearch; // Callback function
  }
  
  render() {
    // Returns search input element
  }
  
  handleInput(event) {
    // Debounced search execution
  }
}
```

**Features:**
- Debounced input (300ms delay)
- Clear button
- Search icon
- Placeholder text
- Keyboard accessibility (Enter key support)

#### 3. FilterSystem Component

Provides filtering controls for category, rating, and popularity.

**Interface:**
```javascript
class FilterSystem {
  constructor(onFilterChange) {
    this.onFilterChange = onFilterChange;
    this.activeFilters = {
      category: null,
      minRating: null,
      sortBy: null
    };
  }
  
  render() {
    // Returns filter controls
  }
  
  applyFilters() {
    // Triggers callback with active filters
  }
  
  resetFilters() {
    // Clears all filters
  }
}
```

**Filter Types:**
- Category dropdown: beaches, temples, waterfalls, culture, hiking, food spots
- Rating filter: 3+, 4+, 5 stars
- Sort options: popularity (review count), rating

#### 4. Gallery Component

Displays destination images with thumbnail navigation.

**Interface:**
```javascript
class Gallery {
  constructor(images) {
    this.images = images; // Array of image objects
    this.currentIndex = 0;
  }
  
  render() {
    // Returns gallery HTML
  }
  
  showImage(index) {
    // Updates primary image display
  }
  
  handleThumbnailClick(index) {
    // Changes active image
  }
}
```

**Features:**
- Primary image display
- Thumbnail grid
- Active thumbnail indicator
- Lazy loading for thumbnails
- Alt text for accessibility
- Fallback placeholder images

#### 5. RatingWidget Component

Displays star ratings and review counts.

**Interface:**
```javascript
class RatingWidget {
  constructor(averageRating, reviewCount) {
    this.averageRating = averageRating;
    this.reviewCount = reviewCount;
  }
  
  render() {
    // Returns rating display HTML
  }
  
  renderStars() {
    // Returns star icons (filled/half/empty)
  }
}
```

**Display:**
- Numerical rating (e.g., "4.5")
- Visual stars (filled, half-filled, empty)
- Review count (e.g., "(127 reviews)")
- "No reviews yet" for zero reviews
- ARIA labels for screen readers

#### 6. ReviewForm Component

Provides interface for submitting reviews.

**Interface:**
```javascript
class ReviewForm {
  constructor(destinationId, onSubmit) {
    this.destinationId = destinationId;
    this.onSubmit = onSubmit;
  }
  
  render() {
    // Returns form HTML
  }
  
  handleSubmit(event) {
    // Validates and submits review
  }
  
  showSuccess() {
    // Displays success message
  }
  
  showError(message) {
    // Displays error message
  }
}
```

**Fields:**
- Star rating selector (1-5, required)
- Review text (textarea, max 1000 chars, optional)
- Name field (text input, max 100 chars, optional)
- Submit button

**Validation:**
- Rating is required
- Text length limits enforced
- Name length limit enforced

#### 7. MapComponent

Displays embedded map with destination marker.

**Interface:**
```javascript
class MapComponent {
  constructor(latitude, longitude, name, address) {
    this.latitude = latitude;
    this.longitude = longitude;
    this.name = name;
    this.address = address;
  }
  
  render() {
    // Returns map iframe or fallback
  }
  
  renderFallback() {
    // Returns address text if map fails
  }
}
```

**Features:**
- Embedded Google Maps (or OpenStreetMap)
- Marker at destination coordinates
- Zoom and pan controls
- Destination name in marker
- Fallback to text address on error

### Page Controllers

#### Homepage Controller

Manages homepage state and rendering.

**Responsibilities:**
- Initialize and render hero section
- Render destination grid
- Handle search input
- Handle filter changes
- Manage grid layout (responsive)
- Handle navigation to detail pages

**State:**
```javascript
{
  allDestinations: [],
  filteredDestinations: [],
  searchQuery: '',
  activeFilters: {},
  isLoading: false
}
```

#### Detail Page Controller

Manages destination detail page state and rendering.

**Responsibilities:**
- Load destination data by ID
- Render gallery, details, map
- Render reviews and rating breakdown
- Handle review submission
- Update ratings after new review
- Manage back navigation

**State:**
```javascript
{
  destination: {},
  reviews: [],
  isSubmitting: false,
  showSuccessMessage: false
}
```

### Utility Modules

#### Router

Simple client-side routing for navigation.

**Interface:**
```javascript
class Router {
  static navigateToDetail(destinationId) {
    // Updates URL and loads detail page
  }
  
  static navigateToHome(preserveFilters = false) {
    // Returns to homepage
  }
  
  static getCurrentRoute() {
    // Parses URL and returns route info
  }
}
```

#### Validators

Input validation functions.

**Functions:**
- `validateRating(rating)`: Ensures rating is 1-5
- `validateReviewText(text)`: Checks length <= 1000
- `validateName(name)`: Checks length <= 100
- `validateCoordinates(lat, lng)`: Validates Bali boundaries
- `sanitizeInput(text)`: Prevents XSS

#### Helpers

Utility functions for common operations.

**Functions:**
- `calculateAverageRating(reviews)`: Computes mean rating
- `formatRating(rating)`: Formats to 1 decimal place
- `debounce(func, delay)`: Debounces function calls
- `lazyLoadImages()`: Implements intersection observer
- `generateId()`: Creates unique IDs
- `formatDate(timestamp)`: Formats review dates

## Data Models

### Destination Model

```javascript
{
  id: string,                    // Unique identifier (e.g., "tanah-lot-temple")
  name: string,                  // Display name
  category: string,              // One of: beaches, temples, waterfalls, culture, hiking, food spots
  shortDescription: string,      // Brief description (max 150 chars)
  fullDescription: string,       // Detailed description
  location: string,              // Location name (e.g., "Tabanan Regency")
  coordinates: {
    latitude: number,            // -8.1 to -8.9 (Bali bounds)
    longitude: number            // 114.4 to 115.7 (Bali bounds)
  },
  images: [
    {
      url: string,               // Image path
      alt: string,               // Alt text for accessibility
      isPrimary: boolean         // Primary image flag
    }
  ],
  practicalInfo: {
    openingHours: string,        // e.g., "6:00 AM - 7:00 PM"
    estimatedCost: string,       // e.g., "IDR 60,000"
    travelTips: string[]         // Array of tip strings
  },
  averageRating: number,         // Calculated average (0-5, 1 decimal)
  reviewCount: number,           // Total number of reviews
  ratingBreakdown: {
    5: number,                   // Count of 5-star reviews
    4: number,                   // Count of 4-star reviews
    3: number,                   // Count of 3-star reviews
    2: number,                   // Count of 2-star reviews
    1: number                    // Count of 1-star reviews
  }
}
```

### Review Model

```javascript
{
  id: string,                    // Unique review identifier
  destinationId: string,         // Associated destination ID
  rating: number,                // 1-5 stars (required)
  reviewText: string,            // Written review (max 1000 chars, optional)
  reviewerName: string,          // Reviewer name (max 100 chars, optional, default: "Anonymous")
  timestamp: number              // Unix timestamp of submission
}
```

### Storage Schema

Data is stored in browser Local Storage with the following keys:

- `bali_destinations`: JSON array of all destination objects
- `bali_reviews`: JSON array of all review objects
- `bali_last_updated`: Timestamp of last data update

### Data Initialization

On first load, the application:
1. Checks for existing data in Local Storage
2. If not found, loads from `data/initial-data.json`
3. Stores in Local Storage for persistence
4. Subsequent loads read from Local Storage

### Data Access Layer

```javascript
class DataStore {
  static getDestinations() {
    // Returns all destinations
  }
  
  static getDestinationById(id) {
    // Returns single destination
  }
  
  static getReviewsByDestinationId(destinationId) {
    // Returns reviews for destination
  }
  
  static addReview(review) {
    // Adds new review and updates destination ratings
  }
  
  static updateDestinationRating(destinationId) {
    // Recalculates and updates average rating
  }
  
  static searchDestinations(query) {
    // Returns destinations matching search
  }
  
  static filterDestinations(filters) {
    // Returns filtered destinations
  }
}
```


## UI Design Specifications

### Color Palette

Following the requirements for a clean, tourism-focused aesthetic:

- **Primary Background**: White (#FFFFFF)
- **Accent/Highlights**: Orange (#FF6B35, #F77F00)
- **Text Accent**: Brown (#6B4423, #8B4513)
- **Text Primary**: Dark Gray (#333333)
- **Text Secondary**: Medium Gray (#666666)
- **Borders**: Light Gray (#E5E5E5)
- **Success**: Green (#10B981)
- **Error**: Red (#EF4444)

### Typography

Using system fonts for performance:

- **Headings**: font-family: 'Inter', system-ui, sans-serif
  - H1: 2.5rem (mobile), 3.5rem (desktop), bold
  - H2: 2rem (mobile), 2.5rem (desktop), bold
  - H3: 1.5rem, semibold
- **Body**: font-family: 'Inter', system-ui, sans-serif
  - Regular: 1rem, normal weight
  - Small: 0.875rem

### Responsive Breakpoints

- **Mobile**: 320px - 767px (single column)
- **Tablet**: 768px - 1023px (two columns)
- **Desktop**: 1024px+ (three-four columns)

### Component Styling Guidelines

#### Destination Card
- Card: white background, subtle shadow, rounded corners (8px)
- Image: aspect ratio 16:9, object-fit cover
- Padding: 1rem
- Hover: slight elevation increase, orange border

#### Buttons
- Primary: orange background, white text, rounded (6px)
- Minimum size: 44x44px (touch-friendly)
- Hover: darker orange
- Disabled: gray with reduced opacity

#### Form Inputs
- Border: 1px solid light gray
- Focus: orange border, outline
- Error: red border
- Padding: 0.75rem
- Border radius: 6px

### Layout Patterns

#### Homepage Layout

```
┌─────────────────────────────────────────┐
│           Hero Section                   │
│  (Full-width image + tagline)           │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│         Search Bar (centered)            │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│         Filter System                    │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│                                          │
│    Destination Grid (responsive)        │
│    [Card] [Card] [Card] [Card]         │
│    [Card] [Card] [Card] [Card]         │
│                                          │
└─────────────────────────────────────────┘
```

#### Detail Page Layout

```
┌─────────────────────────────────────────┐
│         Gallery (primary + thumbs)       │
└─────────────────────────────────────────┘
┌──────────────────┬──────────────────────┐
│                  │                       │
│  Destination     │   Practical Info      │
│  Details         │   - Hours             │
│  - Name          │   - Cost              │
│  - Location      │   - Tips              │
│  - Description   │                       │
│                  │   Map Component       │
│                  │                       │
└──────────────────┴──────────────────────┘
┌─────────────────────────────────────────┐
│         Rating Summary                   │
│  (Average + breakdown)                   │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│         Review Form                      │
└─────────────────────────────────────────┘
┌─────────────────────────────────────────┐
│         Reviews List                     │
│  [Review] [Review] [Review]             │
└─────────────────────────────────────────┘
```

## Navigation Flow

### User Journey: Homepage to Detail

1. User lands on homepage
2. User sees hero section, search bar, and destination grid
3. User can:
   - Search by text → Grid filters in real-time
   - Apply filters → Grid updates
   - Click destination card → Navigate to detail page
4. Detail page loads with URL parameter (e.g., `/destination.html?id=tanah-lot-temple`)
5. User views details, gallery, map, reviews
6. User can submit review → Page updates with new review and rating
7. User clicks back → Returns to homepage with filters preserved

### Routing Implementation

Using URL parameters for client-side routing:

- Homepage: `/index.html` or `/`
- Detail page: `/destination.html?id={destination-id}`

State preservation:
- Store filter state in sessionStorage
- Restore on back navigation
- Clear on fresh homepage visit

## State Management

### Application State

Centralized state management using a simple state object:

```javascript
const AppState = {
  destinations: [],
  reviews: [],
  currentFilters: {
    searchQuery: '',
    category: null,
    minRating: null,
    sortBy: null
  },
  currentDestination: null,
  isLoading: false,
  error: null
};
```

### State Update Pattern

```javascript
function updateState(updates) {
  Object.assign(AppState, updates);
  render(); // Trigger re-render
}
```

### State Persistence

- **Session State**: Filter selections (sessionStorage)
- **Persistent State**: Destinations and reviews (localStorage)
- **Transient State**: Loading states, errors (memory only)

## Performance Optimization

### Image Optimization

1. **Lazy Loading**: Use Intersection Observer for below-fold images
2. **Responsive Images**: Serve appropriate sizes for device
3. **Format**: Use WebP with JPEG fallback
4. **Compression**: Target 80% quality for balance
5. **Dimensions**: Pre-define aspect ratios to prevent layout shift

### Code Optimization

1. **Minification**: Minify CSS and JS for production
2. **Debouncing**: Search input debounced at 300ms
3. **Event Delegation**: Use event delegation for card clicks
4. **DOM Manipulation**: Batch DOM updates, use DocumentFragment
5. **Caching**: Cache DOM queries and computed values

### Loading Strategy

1. **Critical CSS**: Inline critical styles in `<head>`
2. **Deferred JS**: Load non-critical scripts with `defer`
3. **Resource Hints**: Use `preconnect` for external resources (maps)
4. **Service Worker**: Consider for offline capability (future enhancement)

## Accessibility Implementation

### Semantic HTML

- Use `<nav>`, `<main>`, `<article>`, `<section>`, `<footer>`
- Heading hierarchy: Single `<h1>` per page, logical `<h2>`, `<h3>` structure
- Form labels: Associate `<label>` with inputs using `for` attribute
- Buttons: Use `<button>` for actions, `<a>` for navigation

### ARIA Attributes

- **Rating Widget**: `role="img"`, `aria-label="Rating: 4.5 out of 5 stars"`
- **Search**: `role="search"`, `aria-label="Search destinations"`
- **Filters**: `aria-label` for each filter control
- **Gallery**: `aria-label` for navigation buttons
- **Form Errors**: `aria-describedby` linking errors to inputs
- **Loading States**: `aria-live="polite"` for dynamic updates

### Keyboard Navigation

- All interactive elements focusable with Tab
- Enter/Space activate buttons
- Escape closes modals (if implemented)
- Focus indicators visible (orange outline)
- Skip to main content link

### Color Contrast

- Text on white: Minimum 4.5:1 ratio
- Orange buttons: Ensure sufficient contrast with white text
- Error messages: Red with sufficient contrast
- Focus indicators: High contrast orange

### Screen Reader Support

- Alt text for all images (descriptive, not decorative)
- Form field labels and error messages
- Dynamic content announcements (review submission success)
- Rating information in text format

## SEO Implementation

### Meta Tags

Each page includes:

```html
<title>Bali Tourism - Discover Amazing Destinations</title>
<meta name="description" content="Explore the best beaches, temples, and attractions in Bali. Read reviews and plan your perfect trip.">
<meta name="keywords" content="Bali, tourism, travel, beaches, temples, attractions">
<meta name="viewport" content="width=device-width, initial-scale=1.0">

<!-- Open Graph -->
<meta property="og:title" content="Bali Tourism">
<meta property="og:description" content="Discover amazing destinations in Bali">
<meta property="og:image" content="/images/og-image.jpg">
<meta property="og:url" content="https://balitourism.example.com">
<meta property="og:type" content="website">

<!-- Twitter Card -->
<meta name="twitter:card" content="summary_large_image">
<meta name="twitter:title" content="Bali Tourism">
<meta name="twitter:description" content="Discover amazing destinations in Bali">
<meta name="twitter:image" content="/images/twitter-image.jpg">
```

### Structured Data

Destination detail pages include JSON-LD structured data:

```json
{
  "@context": "https://schema.org",
  "@type": "TouristAttraction",
  "name": "Tanah Lot Temple",
  "description": "Ancient Hindu temple on a rock formation...",
  "image": "https://balitourism.example.com/images/tanah-lot.jpg",
  "address": {
    "@type": "PostalAddress",
    "addressLocality": "Tabanan Regency",
    "addressRegion": "Bali",
    "addressCountry": "ID"
  },
  "geo": {
    "@type": "GeoCoordinates",
    "latitude": "-8.6211",
    "longitude": "115.0868"
  },
  "aggregateRating": {
    "@type": "AggregateRating",
    "ratingValue": "4.5",
    "reviewCount": "127"
  }
}
```

### URL Structure

- Homepage: `/` or `/index.html`
- Destination: `/destination.html?id=tanah-lot-temple`
- Semantic IDs: Use kebab-case (e.g., `tanah-lot-temple`, `ubud-monkey-forest`)

### robots.txt

```
User-agent: *
Allow: /
Sitemap: https://balitourism.example.com/sitemap.xml
```

## Error Handling

### Error Categories

1. **Data Loading Errors**: Failed to load destinations or reviews
2. **Validation Errors**: Invalid form input
3. **Network Errors**: Map loading failures
4. **Not Found Errors**: Invalid destination ID
5. **Storage Errors**: localStorage quota exceeded

### Error Handling Strategy

#### Data Loading Errors

```javascript
try {
  const destinations = DataStore.getDestinations();
} catch (error) {
  displayError('Unable to load destinations. Please refresh the page.');
  logError(error);
}
```

#### Form Validation Errors

- Display inline error messages below fields
- Highlight invalid fields with red border
- Prevent form submission until valid
- Clear errors on input change

#### Map Loading Errors

- Fallback to text address display
- Show message: "Map unavailable. Address: [location]"

#### Not Found Errors

- Redirect to homepage with message
- "Destination not found. Showing all destinations."

#### Storage Errors

- Graceful degradation: Continue without persistence
- Show warning: "Unable to save data locally"

### User Feedback

- **Loading States**: Spinner or skeleton screens
- **Success Messages**: Green banner with checkmark
- **Error Messages**: Red banner with error icon
- **Empty States**: Friendly message with suggestions



## Correctness Properties

A property is a characteristic or behavior that should hold true across all valid executions of a system—essentially, a formal statement about what the system should do. Properties serve as the bridge between human-readable specifications and machine-verifiable correctness guarantees.

### Property 1: Destination card completeness

*For any* destination object, when rendered as a destination card, the card HTML should contain the destination image, name, short description, location, average rating, and review count.

**Validates: Requirements 1.5**

### Property 2: Search filtering correctness

*For any* search query string, the filtered results should only include destinations where the query appears (case-insensitive) in either the destination name or description.

**Validates: Requirements 2.1, 2.5**

### Property 3: Search input length acceptance

*For any* text string up to 100 characters, the search bar should accept the input without truncation or error.

**Validates: Requirements 2.4**

### Property 4: Category filter correctness

*For any* category selection, the filtered results should only include destinations that have that exact category value.

**Validates: Requirements 3.1**

### Property 5: Rating filter correctness

*For any* rating threshold (3, 4, or 5), the filtered results should only include destinations with average rating greater than or equal to the threshold.

**Validates: Requirements 3.2**

### Property 6: Popularity sort correctness

*For any* set of destinations, when sorted by popularity, the results should be ordered by review count in descending order.

**Validates: Requirements 3.3**

### Property 7: Multiple filter combination

*For any* combination of active filters (category, rating, search), the results should satisfy all filter criteria simultaneously (AND logic).

**Validates: Requirements 3.4**

### Property 8: Navigation to detail page

*For any* destination card clicked, the application should navigate to the detail page with the correct destination ID in the URL.

**Validates: Requirements 4.1, 4.3**

### Property 9: Filter state preservation

*For any* filter state on the homepage, when navigating to a detail page and back, the filter state should be preserved and the same filtered results should be displayed.

**Validates: Requirements 4.4**

### Property 10: Detail page completeness

*For any* destination, the detail page should display all required information: gallery with images, name, location, full description, practical information (hours, cost, tips), map component, average rating, review count, rating breakdown, and all reviews.

**Validates: Requirements 5.1, 5.2, 5.3, 5.4, 5.5, 5.6, 5.7, 5.8**

### Property 11: Average rating calculation

*For any* non-empty set of reviews, the calculated average rating should equal the sum of all rating values divided by the count of reviews, rounded to one decimal place.

**Validates: Requirements 6.6, 8.5**

### Property 12: Rating widget display completeness

*For any* destination with reviews, the rating widget should display the numerical average rating (one decimal place), visual star representation, and total review count.

**Validates: Requirements 6.1, 6.2, 6.3**

### Property 13: Rating breakdown accuracy

*For any* destination, the rating breakdown should show the correct count of reviews for each star level (1-5), and the sum of all breakdown counts should equal the total review count.

**Validates: Requirements 6.4, 5.7**

### Property 14: Review text length validation

*For any* review text input up to 1000 characters, the form should accept the input; for any input exceeding 1000 characters, the form should reject or truncate it.

**Validates: Requirements 7.3**

### Property 15: Reviewer name length validation

*For any* reviewer name input up to 100 characters, the form should accept the input; for any input exceeding 100 characters, the form should reject or truncate it.

**Validates: Requirements 7.4**

### Property 16: Review submission persistence

*For any* valid review (with rating 1-5), when submitted, the review should be saved to storage, associated with the correct destination, appear in the destination's review list, and trigger a success message.

**Validates: Requirements 7.6, 7.8, 7.9**

### Property 17: Rating update after review submission

*For any* destination, when a new review is submitted, the system should recalculate the average rating, update the displayed rating value, increment the review count, and update the rating breakdown to reflect the new review's star level.

**Validates: Requirements 8.1, 8.2, 8.3, 8.4**

### Property 18: Gallery primary image display

*For any* destination with at least one image, the gallery should display one image prominently as the primary image.

**Validates: Requirements 9.1**

### Property 19: Gallery thumbnail display

*For any* destination with multiple images (more than one), the gallery should display thumbnail images for all images.

**Validates: Requirements 9.2**

### Property 20: Gallery thumbnail interaction

*For any* thumbnail image clicked in the gallery, that image should become the new primary (prominently displayed) image.

**Validates: Requirements 9.3**

### Property 21: Image alt text presence

*For any* image element rendered on the website, the element should have an alt attribute with descriptive text.

**Validates: Requirements 9.4, 12.1**

### Property 22: Map component display

*For any* destination with valid coordinates, the map component should render with an embedded map showing the location, a marker at the coordinates, and the destination name in the marker.

**Validates: Requirements 10.1, 10.2, 10.4**

### Property 23: Responsive grid layout

*For any* viewport width, the destination card grid should display in the appropriate column layout: single column for mobile (320-767px), two columns for tablet (768-1023px), and three-four columns for desktop (1024px+).

**Validates: Requirements 11.3, 11.4, 11.5**

### Property 24: Touch-friendly button sizing

*For any* interactive button on mobile viewports, the button should have minimum dimensions of 44x44 pixels for touch accessibility.

**Validates: Requirements 11.7**

### Property 25: Keyboard navigation support

*For any* interactive element (button, link, input, select), the element should be focusable and operable via keyboard (Tab, Enter, Space keys).

**Validates: Requirements 12.2**

### Property 26: Semantic HTML structure

*For any* page rendered, the HTML should use semantic elements (nav, main, article, section, header, footer) for structural markup rather than generic divs.

**Validates: Requirements 12.4**

### Property 27: ARIA labels for interactive components

*For any* interactive component (search bar, filters, rating widget, form controls), the component should have appropriate ARIA attributes (aria-label, aria-describedby, role) for screen reader accessibility.

**Validates: Requirements 12.5, 12.6**

### Property 28: Form error message association

*For any* form validation error, the error message should be programmatically associated with the corresponding form field using aria-describedby or similar mechanism.

**Validates: Requirements 12.7**

### Property 29: Data caching on navigation

*For any* destination data loaded, when navigating away and returning, the data should be retrieved from cache rather than reloaded, unless explicitly refreshed.

**Validates: Requirements 13.5**

### Property 30: SEO meta tags presence

*For any* page rendered, the HTML head should include meta description, title tag with descriptive text, and Open Graph meta tags for social sharing.

**Validates: Requirements 14.1, 14.2, 14.5**

### Property 31: Structured data for destinations

*For any* destination detail page, the HTML should include JSON-LD structured data markup with TouristAttraction schema including name, description, location, coordinates, and aggregate rating.

**Validates: Requirements 14.3**

### Property 32: Semantic URL generation

*For any* destination, the detail page URL should include a semantic, human-readable identifier (kebab-case) rather than numeric or random IDs.

**Validates: Requirements 14.4**

### Property 33: Heading hierarchy correctness

*For any* page rendered, the heading tags (h1, h2, h3, h4, h5, h6) should appear in hierarchical order without skipping levels, with exactly one h1 per page.

**Validates: Requirements 14.7**

### Property 34: Destination data model completeness

*For any* destination object in storage, the object should contain all required fields: id, name, category, description, location, coordinates, images array, and practical info (opening hours, estimated cost, travel tips).

**Validates: Requirements 15.1**

### Property 35: Review data model completeness

*For any* review object in storage, the object should contain all required fields: id, destinationId, rating (1-5), timestamp, and optional fields reviewText and reviewerName.

**Validates: Requirements 15.3**

### Property 36: Review-destination association

*For any* review in storage, the review's destinationId should reference a valid destination that exists in the destination data store.

**Validates: Requirements 15.4**

### Property 37: Coordinate boundary validation

*For any* destination coordinates, the latitude should be between -8.9 and -8.1 (Bali's latitude range) and longitude should be between 114.4 and 115.7 (Bali's longitude range).

**Validates: Requirements 15.6**

