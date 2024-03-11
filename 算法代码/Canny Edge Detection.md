```
from PIL import Image, ImageDraw # pillow package
import numpy as np
from scipy import ndimage

def read_img_as_array(file):
    '''read image and convert it to numpy array with dtype np.float64'''
    img = Image.open(file)
    arr = np.asarray(img, dtype=np.float64)
    return arr

def save_array_as_img(arr, file):
    
    # make sure arr falls in [0,255]
    min, max = arr.min(), arr.max()
    if min < 0 or max > 255:
        arr = (arr - min)/(max-min)*255

    arr = arr.astype(np.uint8)
    img = Image.fromarray(arr)
    img.save(file)

def show_array_as_img(arr):
    min, max = arr.min(), arr.max()
    if min < 0 or max > 255:  # make sure arr falls in [0,255]
        arr = (arr - min)/(max-min)*255

    arr = arr.astype(np.uint8)
    img = Image.fromarray(arr)
    img.show()

def rgb2gray(arr):
    R = arr[:, :, 0] # red channel
    G = arr[:, :, 1] # green channel
    B = arr[:, :, 2] # blue channel
    gray = 0.2989*R + 0.5870*G + 0.1140*B
    return gray

def sobel(arr):
    '''Apply sobel operator on arr and return the result.'''
    Gx = ndimage.sobel(arr, 0)
    Gy = ndimage.sobel(arr, 1)
    G = np.sqrt(np.square(Gx) + np.square(Gy))
    return G, Gx, Gy

def nonmax_suppress(G, Gx, Gy):
    '''Suppress non-max value along direction perpendicular to the edge.'''
    assert G.shape == Gx.shape
    assert G.shape == Gy.shape
    row_max, col_max = G.shape
    G_result = G.copy()
    theta = np.arctan2(Gy, Gx) * 180 / np.pi
    for row in range(1, row_max-1):
        for col in range(1, col_max - 1):
            if (theta[row][col] >= -22.5 and theta[row][col] <= 22.5) \
                or (theta[row][col] >= 157.5 and theta[row][col] <= 180) \
                or (theta[row][col] >= -180 and theta[row][col] <= -157.5):
                N1 = G_result[row][col+1]
                N2 = G_result[row][col-1]
            elif (theta[row][col] >= 22.5 and theta[row][col] <= 67.5) \
                or (theta[row][col] >= -157.5 and theta[row][col] <= -112.5):
                N1 = G_result[row][col + 1]
                N2 = G_result[row][col - 1]
            elif (theta[row][col] >= 67.5 and theta[row][col] <= 112.5) \
                or (theta[row][col] >= -112.5 and theta[row][col] <= -67.5):
                N1 = G_result[row][col + 1]
                N2 = G_result[row][col - 1]
            else:
                N1 = G_result[row][col + 1]
                N2 = G_result[row][col - 1]
            if G_result[row][col] < N1 or G_result[row][col] < N2:
                G_result[row][col] = 0

    return G_result

def thresholding(G, t):
    '''Binarize G according threshold t'''
    G_binary = G.copy()
    row_max, col_max = G.shape
    for row in range(row_max):
        for col in range(col_max):
            if G[row][col] < t:
                G_binary[row][col] = 0

    return G_binary

def hysteresis_thresholding(G, low, high):
    '''Binarize G according threshold low and high'''
    G_low = thresholding(G, low)
    G_high = thresholding(G, high)

    G_hyst = G_low.copy()
    row_max, col_max = G.shape
    for row in range(1, row_max-1):
        for col in range(1, col_max-1):
            if G_hyst[row][col] >= low and G_hyst[row][col] < high:
                if not (G_hyst[row-1][col] >= high or
                        G_hyst[row+1][col] >= high or
                        G_hyst[row][col-1] >= high or
                        G_hyst[row][col+1] >= high or
                        G_hyst[row-1][col-1] >= high or
                        G_hyst[row-1][col+1] >= high or
                        G_hyst[row+1][col-1] >= high or
                        G_hyst[row+1][col+1] >= high):
                    G_hyst[row][col] = 0

    return G_low, G_high, G_hyst
```