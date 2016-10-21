# Asset handling

Sanity provides a smooth interface and pipeline for dealing with your image and file assets. The architecture looks something like this:

[nice illustraion of asset pipeline]


## Asset types

Assets come in two flavors: `Image` and `File`.

### Image

Use the `Image` asset type to enable upload of images. After upload, you can request resized versions of the original by appending [accordant query params](//sanity.io/docs/api/assets#image-resize) [source [here make it look better in some doc somewhere](https://github.com/rexxars/mead/blob/master/src/parameters/fromQueryString.js#L9)]. E.g. `?w=200&h=150` for a 200 wide and 150 pixel high image.

Currently supported image formats are PNG, JPG, BMP, GIF, TIFF, GIF, SVG and PSD.

### File

Use the `File` asset type to enable upload of any non-image assets, e.g. a PDF user manual, a Markdown minutes of a meeting, an XLS spreadheet or a ZIP bundle of data.

### Other types

In the future, we'll probably support audio and video files in clever ways, but currently all non-image files fall in the `File` category.


## Upload

There are two typical approaches to uploading an asset:

1. User upload: While editing a document, an author uploads an image to be published in the document.
2. Scripted upload: While migrating data, a script uploads multiple images as part of a document store.


### User upload

If the [data schema](//sanity.io/docs/cookbook/schema) for your Sanity Studio has defined an `Image` field on an editable data type, an author who is editing such a document will be presented with a simple UI to add an image. The authors web browser will immediately upload the image to the Sanity cloud storage and promptly make it available in any aspect ratio and resolution.

### Scripted upload

Images and files are uploaded through `api.sanity.io`. To learn how to use it, have a look the [API doc](//sanity.io/docs/api/assets) or check out these two examples.

[curl example]

[node code example - illustrate using request package?]


## Delete

Delete is handled much the same way as upload.

[User clicks delete --> poof]

[Script does delete request --> poof]


## Regarding mime-types...

In the wild, there is no guaranteed way of detecting what kind of content a file has. Therefore, the `api.sanity.io/v1/assets` endpoint requires mime-type as a parameter; the client must make a claim as to what sort of data is incoming.

When a user selects an asset for upload, the web browser will do a best guess, based on file name extension and file content. In edge cases, e.g. the file has a wrong or all together missing extension, different browsers might guess at different mime-types.

These cases should be rare, but if you experience this sort of behavior, contact us and we'll do our best to figure out a viable solution.
