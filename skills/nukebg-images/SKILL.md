---
name: nukebg-images
description: Remove image backgrounds, put products on pure white or marketplace sizes (Amazon, Shopify, Etsy, eBay), remove objects from photos and upscale images with NukeBG. Use when the user wants a transparent PNG, a cutout, a white-background product photo, a marketplace-ready image, an object erased, or an image enlarged.
license: MIT
compatibility: Needs the NukeBG MCP server (https://api.nukebg.com/mcp) or network access to api.nukebg.com, and a NukeBG API key in NUKEBG_API_KEY.
---

# NukeBG image tools

NukeBG removes image backgrounds and prepares product photos. Use it when the user asks to cut out a subject, make a background transparent or white, size images for a marketplace, erase something from a photo, or upscale an image.

## With the MCP server (preferred)

If the `nukebg` MCP tools are available, call them directly:

| Tool | Use it for | Cost |
|---|---|---|
| `remove_background` | Transparent PNG/WebP, or the subject on `white`, a hex colour, `gradient:#a,#b` or `scene:<id>`; `size` for marketplace presets (`amazon`, `shopify`, `etsy`, `ebay`, `instagram`, `story`); `shadow`, `crop`, `subject: "main"` | 1 credit (hd 2, ultra 4) |
| `erase_objects` | Remove things described in words, e.g. "the cables on the desk" | 25 credits (enhance 40, paid credits) |
| `upscale_image` | Enlarge 2× (source up to 16 MP) or 4× (up to 4 MP) | 2 or 4 credits, paid credits |
| `get_credits` | Check the balance before a large job | free |

Pass `image_url` when the image is at a public URL; otherwise send `image_base64`.

Good defaults:
- Product photo for Amazon: `background: "white"`, `size: "amazon"`, `shadow: "soft"`.
- Logo or graphic for the web: `background: "transparent"`, `format: "png"`, `crop: true`.
- Busy photo with stray objects: `subject: "main"`.

## Without the MCP server

Call the REST API with the key in the `X-Api-Key` header:

```bash
curl -f https://api.nukebg.com/v1/cutouts \
  -H "X-Api-Key: $NUKEBG_API_KEY" \
  -F image_file=@photo.jpg \
  -F background=white -F size=amazon \
  -o photo-amazon.jpg
```

Send `Accept: application/json` to get `result_b64` back instead of the file. Batches of up to 500 images, object removal, upscaling and video run through `/v1/batches` and `/v1/jobs`; see https://nukebg.com/docs.

Existing code written for the remove.bg API works unchanged against `https://api.nukebg.com/v1.0/removebg` with a NukeBG key.

## Notes

- Every account gets 50 free credits plus 20 a month. Free credits export up to 2 MP; hd, ultra, upscaling and enhanced erase need paid credits.
- Failed images are never charged.
- If there is no key, point the user to https://nukebg.com/account to create one.
- Only edit images the user owns or has the rights to edit.
