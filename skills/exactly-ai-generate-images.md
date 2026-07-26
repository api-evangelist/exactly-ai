---
name: Generate on-brand images from a style model
description: Generate images from a trained style model, optionally guided by reference
  images, then post-process (upscale, vectorize, remove background).
api: openapi/exactly-ai-public-openapi-original.json
operations:
- durer_backend_public_api_api_routes_models_enhance_prompt_for_model
- durer_backend_public_api_api_routes_images_generate_images
- durer_backend_public_api_api_routes_images_get_image
- durer_backend_public_api_api_routes_images_upscale_image
- durer_backend_public_api_api_routes_images_get_image_upscale
- durer_backend_public_api_api_routes_images_vectorize_image
- durer_backend_public_api_api_routes_images_remove_bg_image
---

# Generate on-brand images from a style model

Generate images from a trained style model, optionally guided by reference images, then post-process (upscale, vectorize, remove background).

## Steps
1. *(optional)* **Enhance the prompt** — `POST /public/v1/models/{uid}/enhance_prompt/` (`durer_backend_public_api_api_routes_models_enhance_prompt_for_model`).
2. **Generate** — `POST /public/v1/images/` (`durer_backend_public_api_api_routes_images_generate_images`) with `prompt` and `model_uid`. Optional `num_images` (1-100), `size` [w,h], `quality` (see `available_qualities` on the model), and up to 10 `reference_images` (purpose: sketch/style/reference/instruct/product/character).
3. **Read the result** — `GET /public/v1/images/{uid}/` (`durer_backend_public_api_api_routes_images_get_image`). A per-image `GenerationErrorOut` (`error_code`,`message`) signals a generation failure.
4. **Upscale** — `POST /public/v1/images/{uid}/upscales/` (`durer_backend_public_api_api_routes_images_upscale_image`) to 2K/4K/6K/8K, then poll `GET /public/v1/images/{uid}/upscales/{scale}/`.
5. **Vectorize** — `POST /public/v1/images/{uid}/vectors/` then `GET .../vectors/`. **Remove background** — `POST /public/v1/images/{uid}/remove-bg/` then `GET .../remove-bg/`.

## Rules
- Auth: `Authorization: Bearer <API token>`.
- Post-processing is asynchronous: POST returns 202, poll the matching GET (200 when done, 202 while processing).
- Only generate against a model whose `status` is `ready`.
