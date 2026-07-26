---
name: Train a custom exactly.ai style model
description: Create a style model, upload brand training images, kick off training,
  and poll until it is ready to generate with.
api: openapi/exactly-ai-public-openapi-original.json
operations:
- durer_backend_public_api_api_routes_models_create_model
- durer_backend_public_api_api_routes_models_add_train_image
- durer_backend_public_api_api_routes_models_run_model_training
- durer_backend_public_api_api_routes_models_get_model_training_progress
- durer_backend_public_api_api_routes_models_get_model
---

# Train a custom exactly.ai style model

Create a style model, upload brand training images, kick off training, and poll until it is ready to generate with.

## Steps
1. **Create the model** — `POST /public/v1/models/` (`durer_backend_public_api_api_routes_models_create_model`). Capture the returned model `uid`.
2. **Add training images** — for each brand image, `POST /public/v1/models/{uid}/train_images/` (`durer_backend_public_api_api_routes_models_add_train_image`). As few as 10 reference images are enough. A 400/403 means the image or model state was rejected — inspect `client_message` on the DurerError envelope.
3. **Start training** — `POST /public/v1/models/{uid}/train/` (`durer_backend_public_api_api_routes_models_run_model_training`). The model moves to `status: training`.
4. **Poll progress** — `GET /public/v1/models/{uid}/train/progress/` (`durer_backend_public_api_api_routes_models_get_model_training_progress`) until complete. Then `GET /public/v1/models/{uid}/` (`durer_backend_public_api_api_routes_models_get_model`) shows `status: ready` / `active: true`.
5. To retrain, `POST /public/v1/models/{uid}/draft/` returns the model to draft first.

## Rules
- Auth: `Authorization: Bearer <API token>` on every call (enterprise access). A 401 means a missing/invalid token.
- Long-running: training is asynchronous (submit-then-poll); never assume the model is ready without polling.
- Identifiers are UUIDs (`uid`, `image_uid`).
