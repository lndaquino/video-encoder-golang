# Introduction - Video-Encoder Service
The video encoder service is responsible for encoding a video to streaming format, using Bento4 library. It converts an MP4/MPEG video to small files separing audio and video parts (fragmenting), allowing video to be streamed by a streaming service (subscriber service). After encoding it uploads the generated files to a GCP Bucket using concurrent upload.

## Architecture
Video encoder service is part of a broad sollution of many microservices: Video catalog service (backend), Video catalog Frontend service, Catalog API service, Subscriber service (frontend), Authentication service usign Keycloack, Subscription service and Video Encoder service.
The solution uses RabbitMQ to asynchronous communication and Docker to build images for dev and production envs. It also uses GCP buckets to store videos.

## Usage
You must download your bucket-credential.json from your GCP bucket account and put it in project root.

### Configuring RabbitMQ for dev and tests
You must create 2 queues in RabbitMQ: (1) videos-result (bind with amq.direct with routing key = jobs), where the calling service can read the encoding result and (2) videos-failed (bind with dlx (death letter exchange)). Queue videos is created at RabbitMQ by this service.

### Using service
You must publish a message in video queue in json format with 2 fields: "resource_id" : "video id identification in the calling service" and "file_path" : "video_path_in_gcp_bucket".
You can configure how many workers will consume this queue (parallel video encoding at CONCURRENCY_WORKERS env) and how many uploads threads will perform upload of the fragmented files (CONCURRENCY_UPLOAD env)
