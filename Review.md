# REVIEW

## 1. It runs on my phone
I opened https://alizhantulepa.github.io/mlr-ex08/ on my iPhone, started the
camera and pointed it at a leaf (a photo of a leaf on another screen). The page
classified the live camera and updated the probabilities of all four classes.
Screenshot: screenshot.png (status "Live classification", leaf in the picture,
probabilities on the page).
Found: the page worked at the first try. Fixed later: after uploading a photo,
an old camera frame could overwrite the probabilities and the hidden camera
element stayed visible (CSS `[hidden]` rule and "newest request wins" check
added in index.html).

## 2. The same numbers as the notebook
I uploaded check_image.jpg through "Upload photo" (camera not started), and
also a PNG copy of it.

| class    | page, JPG | page, PNG | check_expected.json |
|----------|-----------|-----------|---------------------|
| apple    | 0.110     | 0.124     | 0.124               |
| cherry   | 0.889     | 0.875     | 0.875               |
| chestnut | 0.000     | 0.000     | 0.0004              |
| maple    | 0.000     | 0.001     | 0.0005              |

With the PNG the page agrees with the notebook to three decimals. With the JPG
the largest difference is 0.014 (cherry 0.889 against 0.875), the same class
wins. In Colab the JPG and the PNG give identical numbers (PIL), so the
difference comes from the browser decoding the JPEG slightly differently,
not from the model or the preprocessing.

## 3. The preprocessing matches my model
In index.html (function toTensor, lines 84-95):
- central square crop: lines 85-86 (side = min(width, height))
- resize to 96 x 96: line 87 (drawImage into a 96 x 96 canvas, SIZE = 96, line 55)
- divided by 255: line 92
- standardized with mean/std: line 92, constants on lines 56-57
  (mean 0.485, 0.456, 0.406; std 0.229, 0.224, 0.225)
- channel order: R, G, B (c = 0, 1, 2), stored channel by channel (CHW), line 92;
  tensor 1 x 3 x 96 x 96 on line 95.
It matches model_info.json (input_size 96, same mean and std, same classes) and
the transform of the notebook (ToTensor + Normalize with the same mean/std; the
photos there are already 96 x 96). The class order apple, cherry, chestnut, maple
(line 54) is the order of train_ds.classes.

## 4. Nothing leaves the phone
The image is not sent anywhere: the page has no fetch or upload code, and the
frame is processed in the browser by onnxruntime-web. The page loads these URLs:
- cdn.jsdelivr.net: the onnxruntime-web script (line 50), it runs the model in the browser
- cdnjs.cloudflare.com: the qrcode-generator script (line 51), it draws the QR code in the browser
- leaf_model.onnx from the same site (line 155), the model itself
Nothing else is requested; only these scripts and the model are downloaded.
