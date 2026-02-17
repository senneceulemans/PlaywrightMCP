# products-test-plan

## Application Overview

Comprehensive test plan for the product overview (catalog) page at https://automationexercise.com/products. Covers listing, search, filtering, sorting, pagination, product quick view and navigation to product details, add-to-cart behavior from the overview, asset handling (images/prices/labels), accessibility, responsiveness, and error/edge cases. Assume a fresh browser state for each scenario and that test data (products) are present. Tests are independent and can be run in any order.

## Test Scenarios

### 1. Products Overview Suite

**Seed:** `tests/seed.spec.ts`

#### 1.1. Happy path: View product listing

**File:** `tests/products/view-listing.spec.ts`

**Steps:**
  1. Assumptions: Fresh, non-authenticated browser session and products exist in the catalog.
    - expect: Page loads and main product grid/list is visible.
  2. Navigate to the products page (seed navigates here).
    - expect: HTTP 200 and page renders product cards.
  3. Verify each product card shows title, thumbnail image, price, and primary action (e.g., View Product / Add to cart).
    - expect: All visible product cards include required info and clickable actions.
  4. Click a product title or image to open the product details page.
    - expect: User is navigated to the product details page for that product.
  5. Success criteria and failure conditions.
    - expect: Success: product list displays and product detail navigation works. Failure: missing product info or broken links.

#### 1.2. Search functionality

**File:** `tests/products/search.spec.ts`

**Steps:**
  1. Assumptions: Fresh browser and catalog contains products with searchable keywords.
    - expect: Search input is present in the header or products page.
  2. Enter a known product keyword and submit search.
    - expect: Results show only products matching the keyword; results count updated.
  3. Search for a non-existent term.
    - expect: A clear 'no results' message appears and suggestions or category links may be offered.
  4. Verify search works with capitalization and partial matches.
    - expect: Search is case-insensitive and supports partial matches when applicable.
  5. Success criteria and failure conditions.
    - expect: Success: relevant products returned; no results case handled gracefully. Failure: unrelated results or errors.

#### 1.3. Filtering and facets (category, price, brand)

**File:** `tests/products/filters.spec.ts`

**Steps:**
  1. Assumptions: Filters/facets exist (category, price range, brand, availability).
    - expect: Filter controls are present and accessible.
  2. Apply a single filter (e.g., category) and verify the product list updates.
    - expect: Only products matching filter are shown and filter state is visible.
  3. Apply multiple filters together and then clear one filter.
    - expect: Combined filters narrow results; clearing a filter expands the result set accordingly.
  4. Edge: Set price range to a boundary value and verify results.
    - expect: Products returned respect price bounds and no products outside range are shown.
  5. Success criteria and failure conditions.
    - expect: Success: filters operate correctly and UI indicates active filters. Failure: inconsistent results or broken filter UI.

#### 1.4. Sorting (price, popularity, newest)

**File:** `tests/products/sorting.spec.ts`

**Steps:**
  1. Assumptions: Sorting control is present (dropdown or buttons).
    - expect: Sort control visible and default sort known (e.g., featured).
  2. Sort by price ascending and verify product order.
    - expect: Prices are in ascending order across the first page of results.
  3. Sort by newest and verify the most recent products appear first.
    - expect: Results are reordered accordingly.
  4. Success criteria and failure conditions.
    - expect: Success: sorting produces stable, reproducible order. Failure: inconsistent order or UI mismatch.

#### 1.5. Pagination and infinite scroll

**File:** `tests/products/pagination.spec.ts`

**Steps:**
  1. Assumptions: Product list uses pagination or infinite scroll when many items exist.
    - expect: Pagination controls or infinite-scroll trigger present.
  2. If pagination: navigate to next/previous pages and verify content changes and page indices update.
    - expect: Next page shows different products; URL may include page param; back/forward works.
  3. If infinite scroll: scroll to bottom and verify new items load without duplicates.
    - expect: New products appended and network requests succeed.
  4. Edge: Request a high page number via URL and verify handled gracefully.
    - expect: Page shows empty state or redirects to last valid page without errors.
  5. Success criteria and failure conditions.
    - expect: Success: pagination/scroll works and pages are bookmarkable. Failure: duplicates, missing items, or crashes.

#### 1.6. Quick view modal behavior

**File:** `tests/products/quick-view.spec.ts`

**Steps:**
  1. Assumptions: Quick view exists on product cards (hover or click action).
    - expect: Quick view button/icon is present on cards.
  2. Open quick view for a product and verify key info (title, price, short description, add-to-cart) is present.
    - expect: Modal opens quickly, content accurate, and actions work.
  3. Attempt to add product to cart from quick view.
    - expect: Item is added to cart and cart count updates; modal can be closed afterwards.
  4. Close the quick view with Escape and via close button; verify focus returns to originating control.
    - expect: Modal closes and focus restored for accessibility.
  5. Success criteria and failure conditions.
    - expect: Success: quick view is usable and accessible. Failure: modal traps focus or actions fail.

#### 1.7. Add to cart from listing

**File:** `tests/products/add-to-cart-listing.spec.ts`

**Steps:**
  1. Assumptions: Add-to-cart button on product card adds item with default options (simple product).
    - expect: Add-to-cart visible and enabled.
  2. Click add-to-cart and verify mini-cart or cart indicator updates.
    - expect: Cart shows the added product with correct name, price, and quantity.
  3. Edge: Attempt to add more items than available stock (if stock visible) and verify error handling.
    - expect: User sees out-of-stock or limit message and cart not updated beyond stock.
  4. Success criteria and failure conditions.
    - expect: Success: cart updates and quantity/price accurate. Failure: incorrect item or no UI feedback.

#### 1.8. Product card content correctness (price, labels, badges)

**File:** `tests/products/card-content.spec.ts`

**Steps:**
  1. Assumptions: Product cards may show sale price, old price, badges (new, sale), and availability.
    - expect: Each product displays consistent pricing and badge formats.
  2. Verify sale price calculation matches displayed discount percentage when both are shown.
    - expect: Displayed discount equals computed discount from list price to sale price.
  3. Image alt text and fallback: intentionally block a product image and verify alt text or fallback image appears.
    - expect: Alt text present and fallback image or placeholder shown.
  4. Success criteria and failure conditions.
    - expect: Success: prices and badges accurate and images accessible. Failure: incorrect pricing or missing alt text.

#### 1.9. Responsive layout and mobile behavior

**File:** `tests/products/responsive.spec.ts`

**Steps:**
  1. Assumptions: Site is responsive; test across typical viewports (desktop, tablet, mobile).
    - expect: UI adapts to viewport and product grid changes columns appropriately.
  2. Verify product cards remain usable on small screens, quick view works (if available), and filters are accessible via mobile controls.
    - expect: Controls collapse into mobile-friendly UI and remain functional.
  3. Touch interactions: tap add-to-cart and open product details.
    - expect: Tap actions work and focus/scroll behavior is correct.
  4. Success criteria and failure conditions.
    - expect: Success: responsive UI usable on all viewports. Failure: overlapping elements or inaccessible controls.

#### 1.10. Performance and lazy-loading of assets

**File:** `tests/products/performance.spec.ts`

**Steps:**
  1. Assumptions: Product images may lazy-load and page should load within reasonable time on test connection.
    - expect: Initial page load completes and first-view assets are prioritized.
  2. Measure time to first meaningful paint and check that images below the fold load after scrolling.
    - expect: Above-the-fold content loads quickly and below-the-fold images load when scrolled into view.
  3. Edge: Slow network simulation to ensure placeholders and progressive loading behave.
    - expect: Placeholders visible and UI does not break under slow connections.
  4. Success criteria and failure conditions.
    - expect: Success: acceptable load times and lazy-loading without layout shift. Failure: heavy blocking resources or broken placeholders.

#### 1.11. SEO and structured data checks

**File:** `tests/products/seo.spec.ts`

**Steps:**
  1. Assumptions: Product listing pages should include unique titles, meta descriptions, and optionally structured data.
    - expect: Check for presence of a page title, meta description, and product schema (JSON-LD) when applicable.
  2. Verify canonical tags and pagination link rels are present for multi-page listings.
    - expect: Canonical and rel=prev/next or proper pagination meta present.
  3. Success criteria and failure conditions.
    - expect: Success: SEO elements present and consistent. Failure: missing metadata or duplicate titles.

#### 1.12. Accessibility checks (WCAG basics)

**File:** `tests/products/accessibility.spec.ts`

**Steps:**
  1. Assumptions: Fresh browser; use keyboard-only navigation and screen reader attributes.
    - expect: All interactive elements reachable by keyboard and have accessible names.
  2. Navigate through filters, product cards, and pagination using Tab and Enter; check ARIA roles on dynamic elements (modals, dropdowns).
    - expect: Focus order logical; modals trap focus correctly and ARIA roles present for dynamic widgets.
  3. Color contrast: verify product price and badges meet contrast thresholds.
    - expect: Text contrast passes WCAG AA for normal text.
  4. Success criteria and failure conditions.
    - expect: Success: basic WCAG checks pass. Failure: missing labels, poor focus order, or contrast failures.

#### 1.13. Error and resilience scenarios (server errors, partial failures)

**File:** `tests/products/errors.spec.ts`

**Steps:**
  1. Assumptions: Able to simulate partial failures (mocking or network throttling).
    - expect: Application shows friendly error states rather than crash.
  2. Simulate product API returning partial data (missing price or image) and observe UI handling.
    - expect: Cards show placeholders or graceful fallbacks; no uncaught exceptions.
  3. Simulate product API 500 and verify global retry/notice behavior.
    - expect: User sees a retry or error banner and the site remains navigable.
  4. Success criteria and failure conditions.
    - expect: Success: graceful error handling. Failure: broken UI or unhandled exceptions.
