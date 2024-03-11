```
def median_filter(img, s):
    '''Perform median filter of size s x s to image 'arr', and return the filtered image.'''
    median = img.copy()
    s = int(s/2)
    row, col, _ = median.shape
    for y in range(row):
        for x in range(col):
            xl = max(0, x-s)
            xr = min(col-1, x+s)
            yl = max(0, y-s)
            yr = min(row-1, y+s)
            median[y, x, 0] = np.median(median[yl:yr+1, xl:xr+1, 0].ravel())
            median[y, x, 1] = np.median(median[yl:yr+1, xl:xr+1, 1].ravel())
            median[y, x, 2] = np.median(median[yl:yr+1, xl:xr+1, 2].ravel())

    return median
```