## SketchCanvas

```java
public class SketchCanvas extends View 
```

### Attributes
```java
    private ArrayList<SketchData> mPaths = new ArrayList<SketchData>();
    private SketchData mCurrentPath = null;

    private ThemedReactContext mContext;
    private boolean mDisableHardwareAccelerated = false; //(*)

    private Paint mPaint = new Paint(); //(*) Doesn't it belong to SketchData??
    private Bitmap mDrawingBitmap = null, mTranslucentDrawingBitmap = null;
    private Canvas mDrawingCanvas = null, mTranslucentDrawingCanvas = null;

    private boolean mNeedsFullRedraw = true;

    private int mOriginalWidth, mOriginalHeight;
    private Bitmap mBackgroundImage;
    private String mContentMode;

    private ArrayList<CanvasText> mArrCanvasText = new ArrayList<CanvasText>();
    private ArrayList<CanvasText> mArrTextOnSketch = new ArrayList<CanvasText>();
    private ArrayList<CanvasText> mArrSketchOnText = new ArrayList<CanvasText>();
```

### Methods

```java
public boolean openImageFile(String filename, String directory, String mode)
```
- Load image and set it to the `mBackgroundImage` Bitmap attribute and set the `mOriginalHeight` (and Width) and `mContentMode`.
- If the file is not available, fill it with context.


```java
public void setCanvasText(ReadableArray aText)
```
- PASS! because it is about text

```java
public void clear()
```
- Clear `mPaths`
- invalidate the canvas(urgent).

```java
public void newPath(int id, int strokeColor, float strokeWidth)
```
- add a new path to the `mPath` and set the `mCurrentPath`.
- If `strokeColor` is transparent (0x00000000) and `mDisableHardwareAccelerated`, make it true and `setLayerType(View.LAYER_TYPE_SOFTWARE, null)`.

```java
public void addPoint(float x, float y)
```
- add a point to current path and draw.

```java
public void addPath(int id, int strokeColor, float strokeWidth, ArrayList<PointF> points)
```
- add a new `SketchData` and draw it to the `mDrawingCanvas` if the `id` is not found in the current SketchData array.

```java
public void deletePath(int id) 
```
- Delete the corresponding `id` from `mPaths` array

```java
public void end()
```
- Draw the `mCurrentPath` and make it null.
- Fill with `color.TRANSPARENT` on the `mTranslucentDrawingCanvas` with Multiply mode (Just covering all the pixcel to zero?)

## SketchData class
### Attributes
```java
    public final ArrayList<PointF> points = new ArrayList<PointF>(); //Array of android.graphics.PointF
    public final int id, strokeColor;	// identifier and color in RGBa
    public final float strokeWidth;		
    public final boolean isTranslucent; // Transparent?

    private Paint mPaint;	// android.graphics.Paint
    private Path mPath;		// android.graphics.Path : 
    private RectF mDirty = null;	// android.graphics.RectF : rectangular coordinate that contains the path dr
```

### Methods
Skipping constructor

```java
	private void addPointToPath(Path path, PointF tPoint, PointF pPoint, PointF point)
```
Draw a quadratic path from mid(`tPoint`, `pPoint`), passing `pPoint` to mid(`pPoint`, `point`)


```java
	public Rect addPoint(PointF p) //android.graphics.Rect (4 int points)
```
Add a new point `p`, draw it by calling `addPointToPath` and update the rectangle coordinate that contains the path.

```java
    public void draw(Canvas canvas)
    public void drawLastPoint(Canvas canvas)
```
They call the private `draw` function.

```java
   private void draw(Canvas canvas, int pointIndex) //android.graphics.Canvas
```
Basically same as `addPointToPath` with the current array of points and then draw it to the `canvas` by `drawPoint`.





## Some important android.graphics


## `android.view.View`
### Overiding `onDraw()`
```java
protected void onDraw(Canvas canvas)
```
`canvas` : The canvas on which the background will be drawn.
Whenever you call `invalidate()`, it calls `onDraw`.


## Some important classes and methods in `android.graphics`

### `android.graphics.Paint` : How to draw
- Color, Belend style, Filter ...

### `android.graphics.Canvas` : What to draw
```java
public void drawColor (long color, BlendMode mode)
```
- Fill the entire canvas' bitmap (restricted to the current clip) with the specified color and blendmode.
- `BlendMode` : Enum that represents how to draw a destination intact onto source intact
	- `CLEAR` : Destination pixels covered by the source are cleared to 0. 
	- `MULTIPLY` : Multiplies the source and destination pixel
	- ... availables are [here](https://developer.android.com/reference/android/graphics/BlendMode)

### `android.graphics.Bitmap` : One of the drawing primitives

### `android.graphics.Path` : Another
Draw geometric paths consisting of straight, quadratic and cubic curves.
```java
	path.moveTo(float x, float y) // : set the beginning of the next contour to the point (x,y)
	path.quadTo(float x1, float y1, float x2, float y2) // : add a quadratic bezier from the last point, approaching control point (x1, y1) and the ending at (x2, y2)
```
