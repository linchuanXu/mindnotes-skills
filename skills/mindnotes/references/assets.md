# Assets Reference

Use this when the user wants to attach an image to a note or purchase record.

## Image Upload

Upload first, then reuse the returned `asset.path`.

```json
{"api_name":"/assets/upload-image","filename":"cover.png","image_base64":"...","skill_version":"1.0.0"}
```

Supported inputs:

| Field | Use |
|---|---|
| `image_base64` | Raw base64 content, optionally a data URL |
| `data_url` | Base64 data URL |
| `image_url` | Public HTTP(S) image URL |
| `filename` | File name with `.jpg`, `.jpeg`, `.png`, `.gif`, or `.webp` |

The response returns:

| Field | Use |
|---|---|
| `asset.path` | Store this in note `image`, note `cover_image`, or purchase `image_url` |
| `asset.url` | User-facing URL path |
| `asset.status` | `success` or `exists` |

Do not store local filesystem paths in notes or purchases. Do not upload unrelated images without user intent.
