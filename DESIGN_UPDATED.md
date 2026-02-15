# Design Document: AI Photobooth

## Overview

The AI Photobooth is a client-side web application built with React, HTML5, and CSS3. The architecture follows a component-based design with clear separation between camera management, photo processing, template rendering, AI enhancement, artistic style transformation, and UI control. The application uses React hooks for state management, the MediaDevices API for camera access, Canvas API for photo composition and rendering, and integrates AI-powered enhancement and Ghibli-style cartoon conversion through client-side image processing algorithms.

The system is designed to be lightweight, responsive, and work entirely in the browser without requiring a backend server. All photo processing happens client-side to ensure privacy and fast performance. React's component model provides clean separation of concerns and efficient re-rendering of UI elements.

## Recent Updates

### Enhanced AI Touchup (Latest)
- Increased color saturation from 1.15x to 1.25x for more noticeable enhancement
- Increased sharpening intensity from 0.5 to 0.7
- Increased skin smoothing from 0.3 to 0.4
- Increased background blur radius from 2px to 3px
- Increased background brightening from 1.05x to 1.1x
- Added detailed console logging for debugging
- Added visual feedback: animated sparkle icon and status text when active

### Countdown Timer Feature
- 3-second initial countdown before first capture
- Automatic capture of up to 5 photos with 3-second intervals
- Full-screen overlay with countdown display
- Proper timer cleanup on component unmount

### Layout Improvements
- 3-column layout: Templates (left) | Camera (center) | Gallery (right)
- Vertical template selector with sticky positioning
- Vertical gallery with sticky positioning
- Responsive design that stacks on mobile

### Download Enhancements
- Three layout options: Vertical, Horizontal, Grid
- Real photo previews generated from captured images
- Layout menu positioned ABOVE download button
- Arrow direction: ▼ when closed, ▲ when open

## Architecture

### High-Level Architecture

```
┌─────────────────────────────────────────────────────────────┐
│                         App Component                        │
│              (Root, State Management via Hooks)              │
└────┬──────────┬──────────┬──────────┬────────────┬─────────┘
     │          │          │          │            │
┌────▼────┐ ┌──▼─────┐ ┌──▼─────┐ ┌──▼──────┐ ┌──▼────────┐
│ Camera  │ │ Photo  │ │Template│ │  Filter │ │  Gallery  │
│Component│ │Capture │ │Selector│ │ Selector│ │ Component │
│         │ │(Timer) │ │(Left)  │ │         │ │  (Right)  │
└────┬────┘ └────┬───┘ └────┬───┘ └────┬────┘ └─────┬─────┘
     │          │          │          │            │
┌────▼──────────▼──────────▼──────────▼────────────▼─────────┐
│                      Custom Hooks Layer                      │
│  (useCamera, usePhotoCapture, useTemplates, useFilters)     │
└────┬──────────┬──────────┬──────────┬────────────┬─────────┘
     │          │          │          │            │
┌────▼────┐ ┌──▼─────┐ ┌──▼─────┐ ┌──▼──────┐ ┌──▼────────┐
│ Camera  │ │ Photo  │ │Template│ │  Filter │ │    AI     │
│ Utils   │ │Processor│ │Renderer│ │ Engine  │ │  Touchup  │
│         │ │         │ │(Layouts)│ │         │ │  +Ghibli  │
└─────────┘ └────────┘ └────────┘ └─────────┘ └───────────┘
```

### React Component Structure

1. **App Component**: Root component managing global state (captured photos, current template, current filter, AI touchup enabled, Ghibli style enabled)
2. **CameraComponent**: Displays video preview and handles camera stream
3. **PhotoCapture**: Capture button with countdown timer and automatic multi-shot logic
4. **TemplateSelector**: UI for selecting photo templates (vertical layout, left sidebar)
5. **FilterSelector**: UI for selecting and applying filters
6. **AITouchupToggle**: Toggle switch for AI enhancement with visual feedback
7. **GhibliStyleToggle**: Toggle switch for Ghibli cartoon conversion (NEW)
8. **GalleryComponent**: Displays captured photos as thumbnails (vertical layout, right sidebar)
9. **DownloadButton**: Handles photo strip generation with layout selection menu

### Custom Hooks

1. **useCamera**: Manages camera access, stream lifecycle, and video element ref
2. **usePhotoCapture**: Handles photo capture logic, countdown timer, and canvas operations
3. **useTemplates**: Manages template selection and configuration
4. **useFilters**: Manages filter selection and application
5. **usePhotoGallery**: Manages captured photos array and gallery operations

### Utility Modules

1. **Camera Utils**: Camera access and stream management functions
2. **Photo Processor**: Canvas-based photo capture and processing
3. **Template Renderer**: Decorative frame and element rendering, photo strip layouts (vertical, horizontal, grid)
4. **Filter Engine**: Filter application logic
5. **AI Touchup**: Image enhancement algorithms (brightness, contrast, saturation, sharpening, skin smoothing, background blur)
6. **Ghibli Style Converter**: Cartoon-style transformation (NEW)

## Components and Interfaces

### App Component

Root component managing global application state.

```typescript
interface AppState {
  capturedPhotos: PhotoMetadata[];
  currentTemplate: string;
  currentFilter: string;
  aiTouchupEnabled: boolean;
  ghibliStyleEnabled: boolean;  // NEW
  maxPhotos: number;
}

function App(): JSX.Element
```

**State Management:**
- Uses `useState` for captured photos, template, filter, AI touchup, and Ghibli style settings
- Passes state and state setters to child components via props
- Coordinates interactions between components

### PhotoCapture Component (Updated)

Handles photo capture with countdown timer functionality.

```typescript
interface PhotoCaptureProps {
  videoRef: RefObject<HTMLVideoElement>;
  currentTemplate: string;
  currentFilter: string;
  aiTouchupEnabled: boolean;
  ghibliStyleEnabled: boolean;  // NEW
  capturedCount: number;
  maxPhotos: number;
  onPhotoCapture: (photo: PhotoMetadata) => void;
}

function PhotoCapture(props: PhotoCaptureProps): JSX.Element
```

**Responsibilities:**
- Render capture button with status text
- Implement 3-second countdown timer
- Capture up to 5 photos automatically with 3-second intervals
- Display full-screen countdown overlay
- Coordinate photo processing pipeline
- Clean up timers on unmount
- Disable button when at max photos or during capture

### GhibliStyleToggle Component (NEW)

Toggle switch for Ghibli cartoon style conversion.

```typescript
interface GhibliStyleToggleProps {
  enabled: boolean;
  onToggle: (enabled: boolean) => void;
}

function GhibliStyleToggle(props: GhibliStyleToggleProps): JSX.Element
```

**Responsibilities:**
- Render toggle switch with Ghibli-themed styling
- Display visual feedback when enabled (animated icon, status text)
- Handle toggle state changes
- Provide user-friendly labels and descriptions

### AITouchupToggle Component (Updated)

Toggle switch for AI touchup feature with enhanced visual feedback.

```typescript
interface AITouchupToggleProps {
  enabled: boolean;
  onToggle: (enabled: boolean) => void;
}

function AITouchupToggle(props: AITouchupToggleProps): JSX.Element
```

**Visual Feedback:**
- Animated sparkle icon (✨) with enhanced animation when active
- Status text changes to "✓ Active - Enhancing photos" when enabled
- Glowing effect on active icon
- Color changes to indicate active state

### DownloadButton Component (Updated)

Handles photo download with layout selection.

```typescript
interface DownloadButtonProps {
  photos: PhotoMetadata[];
  disabled: boolean;
}

function DownloadButton(props: DownloadButtonProps): JSX.Element
```

**Responsibilities:**
- Render download button with arrow indicator (▼ closed, ▲ open)
- Display layout selection menu ABOVE button
- Generate real photo previews for each layout option
- Handle single photo download (direct)
- Handle multiple photo download (with layout selection)
- Trigger browser download

**Layout Options:**
- **Vertical Strip**: Classic photobooth style, photos stacked vertically
- **Horizontal Strip**: Landscape orientation, photos arranged horizontally
- **Grid Layout**: 2x2 or 2x3 grid depending on photo count

### Custom Hooks

#### usePhotoCapture (Updated)

Handles photo capture, countdown timer, and processing.

```typescript
interface UsePhotoCaptureOptions {
  videoRef: RefObject<HTMLVideoElement>;
  template: string;
  filter: string;
  aiTouchupEnabled: boolean;
  ghibliStyleEnabled: boolean;  // NEW
}

interface UsePhotoCaptureReturn {
  capturePhoto: () => Promise<PhotoMetadata>;
  isProcessing: boolean;
  countdown: number | string | null;  // NEW
  isTimerActive: boolean;  // NEW
  startCountdown: () => Promise<void>;  // NEW
}

function usePhotoCapture(options: UsePhotoCaptureOptions): UsePhotoCaptureReturn
```

### Utility Modules

#### AITouchup (Updated)

AI-powered photo enhancement with increased intensity.

```typescript
class AITouchup {
  static async enhance(
    canvas: HTMLCanvasElement,
    options?: EnhanceOptions
  ): Promise<void>;
  
  static autoAdjust(canvas: HTMLCanvasElement): void;
  
  static smoothSkin(
    canvas: HTMLCanvasElement,
    intensity?: number  // Default: 0.4 (increased from 0.3)
  ): void;
  
  static enhanceColors(
    canvas: HTMLCanvasElement,
    saturationFactor?: number  // Default: 1.25 (increased from 1.15)
  ): void;
  
  static sharpenImage(
    canvas: HTMLCanvasElement,
    amount?: number  // Default: 0.7 (increased from 0.5)
  ): void;
  
  static enhanceBackground(
    canvas: HTMLCanvasElement,
    blurRadius?: number,  // Default: 3 (increased from 2)
    brightenFactor?: number  // Default: 1.1 (increased from 1.05)
  ): void;
}
```

**Enhancement Pipeline:**
1. Auto-adjust brightness and contrast
2. Enhance color saturation (1.25x)
3. Sharpen image (0.7 intensity)
4. Smooth skin tones (0.4 intensity)
5. Blur and brighten background (3px blur, 1.1x brightness)

**Console Logging:**
- Logs start of enhancement
- Logs each enhancement step
- Logs completion time
- Warns if processing exceeds 3 seconds

#### GhibliStyleConverter (NEW)

Converts photos to Studio Ghibli cartoon aesthetic.

```typescript
class GhibliStyleConverter {
  static async convert(
    canvas: HTMLCanvasElement,
    options?: GhibliOptions
  ): Promise<void>;
  
  static detectEdges(
    canvas: HTMLCanvasElement,
    threshold?: number
  ): void;
  
  static posterizeColors(
    canvas: HTMLCanvasElement,
    levels?: number  // Default: 10 (8-12 distinct colors)
  ): void;
  
  static enhanceWarmth(
    canvas: HTMLCanvasElement,
    amount?: number
  ): void;
  
  static softenDetails(
    canvas: HTMLCanvasElement,
    radius?: number
  ): void;
  
  static applyCartoonOutline(
    canvas: HTMLCanvasElement,
    edgeCanvas: HTMLCanvasElement
  ): void;
}
```

**Conversion Pipeline:**
1. Detect edges using Sobel operator
2. Posterize colors to 8-12 distinct levels
3. Enhance saturation and warmth
4. Soften details with selective blur
5. Apply cartoon outlines from edge detection
6. Adjust contrast for hand-painted look

**Options:**
```typescript
interface GhibliOptions {
  colorLevels?: number;      // Number of distinct colors (8-12)
  edgeThreshold?: number;    // Edge detection sensitivity (0-255)
  warmthAmount?: number;     // Warmth enhancement (0-1)
  softenRadius?: number;     // Detail softening radius (1-5)
  outlineThickness?: number; // Cartoon outline thickness (1-3)
}
```

#### TemplateRenderer (Updated)

Renders decorative templates and creates photo strips in multiple layouts.

```typescript
class TemplateRenderer {
  static renderTemplate(
    photoCanvas: HTMLCanvasElement,
    templateType: string
  ): HTMLCanvasElement;
  
  static createPhotoStrip(
    canvases: HTMLCanvasElement[],
    size: number,
    layout: 'vertical' | 'horizontal' | 'grid'  // NEW: layout parameter
  ): HTMLCanvasElement;
  
  static createVerticalStrip(
    canvases: HTMLCanvasElement[],
    width: number
  ): HTMLCanvasElement;
  
  static createHorizontalStrip(
    canvases: HTMLCanvasElement[],
    height: number
  ): HTMLCanvasElement;
  
  static createGridStrip(
    canvases: HTMLCanvasElement[],
    cellSize: number
  ): HTMLCanvasElement;
  
  static drawDecorations(
    ctx: CanvasRenderingContext2D,
    decorationType: string,
    positions: Array<{x: number, y: number, size: number}>
  ): void;
  
  static drawRoundedFrame(
    ctx: CanvasRenderingContext2D,
    x: number,
    y: number,
    width: number,
    height: number,
    radius: number,
    color: string
  ): void;
}
```

**Layout Specifications:**
- **Vertical**: Photos stacked top to bottom, gradient background, heart decorations, photo number badges
- **Horizontal**: Photos arranged left to right, landscape orientation, decorative borders
- **Grid**: 2x2 for 4 photos, 2x3 for 5-6 photos, uniform spacing, rounded corners

## Data Models

### PhotoMetadata (Updated)

```javascript
{
  id: string,              // Unique identifier (timestamp-based)
  timestamp: number,       // Capture time (Unix timestamp)
  template: string,        // Template type used
  filter: string,          // Filter applied
  aiEnhanced: boolean,     // Whether AI touchup was applied
  ghibliStyle: boolean,    // Whether Ghibli style was applied (NEW)
  dimensions: {
    width: number,
    height: number
  },
  canvas: HTMLCanvasElement,  // The rendered photo canvas
  dataUrl: string          // Data URL for display/download
}
```

### GhibliOptions (NEW)

```javascript
{
  colorLevels: number,      // Number of distinct colors (default: 10)
  edgeThreshold: number,    // Edge detection sensitivity (default: 50)
  warmthAmount: number,     // Warmth enhancement (default: 0.3)
  softenRadius: number,     // Detail softening radius (default: 2)
  outlineThickness: number  // Cartoon outline thickness (default: 2)
}
```

### AppState (Updated)

```javascript
{
  cameraActive: boolean,
  currentTemplate: string,
  currentFilter: string,
  aiTouchupEnabled: boolean,
  ghibliStyleEnabled: boolean,  // NEW
  capturedPhotos: PhotoMetadata[],
  maxPhotos: number,
  isProcessing: boolean,
  countdown: number | string | null,  // NEW
  isTimerActive: boolean  // NEW
}
```

## Processing Pipeline

### Photo Capture Pipeline (Updated)

```
1. User clicks "Start Capture" button
2. Display 3-second countdown overlay
3. Capture frame from video stream → Canvas
4. Apply selected filter → Modified Canvas
5. IF aiTouchupEnabled:
   - Apply AI enhancement (brightness, colors, sharpening, skin smoothing, background blur)
6. IF ghibliStyleEnabled:  // NEW
   - Apply Ghibli cartoon conversion (edge detection, posterization, warmth, outlines)
7. Apply selected template → Final Canvas
8. Create PhotoMetadata object
9. Add to gallery
10. Wait 3 seconds
11. Repeat steps 3-10 for up to 5 photos total
12. Stop countdown and reset UI
```

### Download Pipeline (Updated)

```
1. User clicks "Download Strip" button
2. IF single photo:
   - Download photo directly
3. IF multiple photos:
   - Display layout selection menu ABOVE button
   - Generate real photo previews for each layout
   - User selects layout (vertical, horizontal, or grid)
   - Create photo strip in selected layout
   - Add decorations and branding
   - Convert to PNG data URL
   - Trigger browser download with descriptive filename
```

## Correctness Properties (Updated)

### Property 26: AI Touchup increases saturation noticeably

*For any* photo canvas, applying AI touchup should increase color saturation by at least 20% (measured by average distance from grayscale).

**Validates: Requirements 5.2**

### Property 27: AI Touchup blurs background

*For any* photo canvas with AI touchup applied, background pixels (detected as non-skin-tone or edge areas) should have reduced high-frequency content compared to the original.

**Validates: Requirements 5.4**

### Property 28: AI Touchup toggle shows visual feedback

*For any* state where AI touchup is enabled, the toggle component should display the active sparkle animation and status text "✓ Active - Enhancing photos".

**Validates: Requirements 5.7**

### Property 29: Countdown timer captures multiple photos

*For any* countdown sequence, the system should capture between 1 and 5 photos (depending on remaining slots) with 3-second intervals.

**Validates: Requirements 2.1, 2.2, 2.3**

### Property 30: Countdown overlay displays during capture

*For any* active countdown or capture sequence, the system should display a full-screen overlay with countdown numbers or capture indicator.

**Validates: Requirements 2.5**

### Property 31: Ghibli style reduces color palette

*For any* photo canvas with Ghibli style applied, the number of distinct colors should be reduced to approximately 8-12 levels.

**Validates: Requirements 6.3**

### Property 32: Ghibli style creates cartoon outlines

*For any* photo canvas with Ghibli style applied, edge pixels should have darker values creating visible outlines.

**Validates: Requirements 6.2**

### Property 33: Ghibli style processes within time limit

*For any* photo canvas, Ghibli style conversion should complete within 3 seconds.

**Validates: Requirements 6.8**

### Property 34: Download button arrow direction matches menu state

*For any* download button state, the arrow should point DOWN (▼) when menu is closed and UP (▲) when menu is open.

**Validates: Requirements 8.9**

### Property 35: Layout previews show real photos

*For any* set of captured photos (2 or more), the layout menu should display preview images generated from those actual photos.

**Validates: Requirements 8.5, 8.10**

### Property 36: Photo strip layout matches selection

*For any* layout selection (vertical, horizontal, grid), the downloaded photo strip should arrange photos in that specific layout pattern.

**Validates: Requirements 8.6**

### Property 37: Timer cleanup on unmount

*For any* active countdown or capture sequence, if the component unmounts, all timers and intervals should be properly cleared.

**Validates: Requirements 2.10**

## Testing Strategy

### Property-Based Testing (Updated)

**Library**: fast-check for JavaScript property-based testing

**New Test Cases:**

```typescript
// Property 26: AI Touchup increases saturation
test('AI touchup increases color saturation noticeably', () => {
  fc.assert(
    fc.property(fc.uint8Array({ minLength: 1000, maxLength: 10000 }), (pixels) => {
      const canvas = createCanvasFromPixels(pixels);
      const originalSaturation = measureSaturation(canvas);
      
      AITouchup.enhanceColors(canvas);
      
      const enhancedSaturation = measureSaturation(canvas);
      return enhancedSaturation >= originalSaturation * 1.2;
    }),
    { numRuns: 100 }
  );
});

// Property 31: Ghibli style reduces color palette
test('Ghibli style reduces colors to 8-12 distinct levels', () => {
  fc.assert(
    fc.property(fc.uint8Array({ minLength: 1000, maxLength: 10000 }), (pixels) => {
      const canvas = createCanvasFromPixels(pixels);
      
      GhibliStyleConverter.convert(canvas);
      
      const distinctColors = countDistinctColors(canvas);
      return distinctColors >= 8 && distinctColors <= 12;
    }),
    { numRuns: 100 }
  );
});

// Property 29: Countdown captures multiple photos
test('countdown timer captures correct number of photos', () => {
  fc.assert(
    fc.property(fc.integer({ min: 0, max: 5 }), async (existingPhotos) => {
      const maxPhotos = 5;
      const expectedCaptures = Math.min(5, maxPhotos - existingPhotos);
      
      const { result } = renderHook(() => usePhotoCapture({
        videoRef: mockVideoRef,
        template: 'cute-basic',
        filter: 'normal',
        aiTouchupEnabled: false,
        ghibliStyleEnabled: false
      }));
      
      await act(async () => {
        await result.current.startCountdown();
      });
      
      return capturedPhotos.length === expectedCaptures;
    }),
    { numRuns: 50 }
  );
});
```

### Unit Testing (Updated)

**New Test Cases:**

```typescript
describe('GhibliStyleConverter', () => {
  it('should posterize colors to specified levels', () => {
    const canvas = createTestCanvas();
    GhibliStyleConverter.posterizeColors(canvas, 10);
    const distinctColors = countDistinctColors(canvas);
    expect(distinctColors).toBeLessThanOrEqual(12);
    expect(distinctColors).toBeGreaterThanOrEqual(8);
  });
  
  it('should complete conversion within 3 seconds', async () => {
    const canvas = createLargeTestCanvas();
    const startTime = performance.now();
    await GhibliStyleConverter.convert(canvas);
    const duration = performance.now() - startTime;
    expect(duration).toBeLessThan(3000);
  });
});

describe('PhotoCapture with Countdown', () => {
  it('should display countdown overlay during capture', async () => {
    render(<PhotoCapture {...mockProps} />);
    const button = screen.getByText(/Start Capture/i);
    
    fireEvent.click(button);
    
    await waitFor(() => {
      expect(screen.getByText('3')).toBeInTheDocument();
    });
  });
  
  it('should clean up timers on unmount', () => {
    const { unmount } = render(<PhotoCapture {...mockProps} />);
    const button = screen.getByText(/Start Capture/i);
    
    fireEvent.click(button);
    unmount();
    
    // Verify no timers are still running
    expect(jest.getTimerCount()).toBe(0);
  });
});

describe('DownloadButton', () => {
  it('should show arrow pointing down when menu is closed', () => {
    render(<DownloadButton photos={mockPhotos} />);
    expect(screen.getByText(/▼/)).toBeInTheDocument();
  });
  
  it('should show arrow pointing up when menu is open', () => {
    render(<DownloadButton photos={mockPhotos} />);
    const button = screen.getByText(/Download Strip/i);
    
    fireEvent.click(button);
    
    expect(screen.getByText(/▲/)).toBeInTheDocument();
  });
  
  it('should generate real photo previews', () => {
    render(<DownloadButton photos={mockPhotos} />);
    const button = screen.getByText(/Download Strip/i);
    
    fireEvent.click(button);
    
    const previews = screen.getAllByAltText(/preview/i);
    expect(previews.length).toBe(3); // vertical, horizontal, grid
    previews.forEach(preview => {
      expect(preview.src).toMatch(/^data:image\/png/);
    });
  });
});
```

## Error Handling (Updated)

### Ghibli Conversion Errors (NEW)

**Error Scenario**: Ghibli conversion fails due to memory constraints or invalid canvas

**Handling Strategy**:
1. Wrap conversion in try-catch
2. Log detailed error to console
3. Display user-friendly message
4. Fall back to original photo without Ghibli style
5. Allow user to retry or disable Ghibli style

**Implementation**:
```javascript
try {
  if (ghibliStyleEnabled) {
    await GhibliStyleConverter.convert(canvas);
  }
} catch (error) {
  console.error('Ghibli conversion failed:', error);
  showWarning('Ghibli style could not be applied. Photo saved without cartoon effect.');
  // Continue with original canvas
}
```

### Timer Cleanup Errors (NEW)

**Error Scenario**: Component unmounts during countdown or capture sequence

**Handling Strategy**:
1. Use useEffect cleanup function
2. Clear all timers and intervals
3. Reset countdown state
4. Prevent memory leaks

**Implementation**:
```javascript
useEffect(() => {
  return () => {
    if (timerRef.current) clearTimeout(timerRef.current);
    if (captureIntervalRef.current) clearInterval(captureIntervalRef.current);
  };
}, []);
```

## Performance Considerations

### Ghibli Style Optimization

- Use Web Workers for heavy computation (edge detection, posterization)
- Process at reduced resolution then upscale for large images
- Cache edge detection results
- Use efficient color quantization algorithms
- Limit processing to visible canvas area

### Countdown Timer Optimization

- Use requestAnimationFrame for smooth countdown display
- Debounce capture button to prevent multiple simultaneous countdowns
- Clean up timers immediately on completion
- Use refs instead of state for timer IDs to avoid re-renders

### Layout Preview Optimization

- Generate previews at small size (100-200px)
- Cache preview images until photos change
- Use useEffect with photos dependency
- Debounce preview generation if photos change rapidly

## Future Enhancements

1. **Additional Artistic Styles**: Watercolor, oil painting, sketch
2. **Custom Ghibli Options**: User-adjustable color levels, outline thickness
3. **Real-time Style Preview**: Show Ghibli effect on live camera feed
4. **Batch Processing**: Apply styles to multiple photos at once
5. **Style Mixing**: Combine multiple artistic effects
6. **Export Options**: Additional formats (JPEG, WebP), quality settings
7. **Social Sharing**: Direct sharing to social media platforms
8. **Photo Editing**: Crop, rotate, adjust brightness/contrast after capture
