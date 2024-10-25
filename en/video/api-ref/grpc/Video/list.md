---
editable: false
sourcePath: en/_api-ref-grpc/video/v1/api-ref/grpc/Video/list.md
---

# Video API, gRPC: VideoService.List {#List}

List videos for channel.

## gRPC request

**rpc List ([ListVideoRequest](#yandex.cloud.video.v1.ListVideoRequest)) returns ([ListVideoResponse](#yandex.cloud.video.v1.ListVideoResponse))**

## ListVideoRequest {#yandex.cloud.video.v1.ListVideoRequest}

```json
{
  "channelId": "string",
  "pageSize": "int64",
  "pageToken": "string",
  "orderBy": "string",
  "filter": "string"
}
```

#|
||Field | Description ||
|| channelId | **string**

ID of the channel. ||
|| pageSize | **int64**

The maximum number of the results per page to return. Default value: 100. ||
|| pageToken | **string**

Page token for getting the next page of the result. ||
|| orderBy | **string**

By which column the listing should be ordered and in which direction,
format is "createdAt desc". "id asc" if omitted.
Possible fields: ["id", "createdAt", "updatedAt"]
Both snake_case and camelCase are supported for fields. ||
|| filter | **string**

Filter expression that filters resources listed in the response.
Expressions are composed of terms connected by logic operators.
Value in quotes: `'` or `"`
Example: "key1='value' AND key2='value'"
Supported operators: ["AND"].
Supported fields: ["title", "status", "visibility_status"]
Both snake_case and camelCase are supported for fields. ||
|#

## ListVideoResponse {#yandex.cloud.video.v1.ListVideoResponse}

```json
{
  "videos": [
    {
      "id": "string",
      "channelId": "string",
      "title": "string",
      "description": "string",
      "thumbnailId": "string",
      "status": "VideoStatus",
      "duration": "google.protobuf.Duration",
      "visibilityStatus": "VisibilityStatus",
      // Includes only one of the fields `tusd`
      "tusd": {
        "url": "string"
      },
      // end of the list of possible fields
      // Includes only one of the fields `publicAccess`, `authSystemAccess`
      "publicAccess": "VideoPublicAccessRights",
      "authSystemAccess": "VideoAuthSystemAccessRights",
      // end of the list of possible fields
      "createdAt": "google.protobuf.Timestamp",
      "updatedAt": "google.protobuf.Timestamp",
      "labels": "string"
    }
  ],
  "nextPageToken": "string"
}
```

#|
||Field | Description ||
|| videos[] | **[Video](#yandex.cloud.video.v1.Video)** ||
|| nextPageToken | **string**

Token for getting the next page. ||
|#

## Video {#yandex.cloud.video.v1.Video}

#|
||Field | Description ||
|| id | **string**

ID of the video. ||
|| channelId | **string**

ID of the channel where the video was created. ||
|| title | **string**

Video title. ||
|| description | **string**

Video description. ||
|| thumbnailId | **string**

ID of the thumbnail. ||
|| status | enum **VideoStatus**

Video status.

- `VIDEO_STATUS_UNSPECIFIED`: Video status unspecified.
- `WAIT_UPLOADING`: Waiting for the whole number of bytes to be loaded.
- `PROCESSING`: Video processing.
- `READY`: Video is ready, processing is completed.
- `ERROR`: An error occurred during video processing. ||
|| duration | **[google.protobuf.Duration](https://developers.google.com/protocol-buffers/docs/reference/csharp/class/google/protobuf/well-known-types/duration)**

Video duration. Optional, may be empty until the transcoding result is ready. ||
|| visibilityStatus | enum **VisibilityStatus**

Video visibility status.

- `VISIBILITY_STATUS_UNSPECIFIED`: Visibility status unspecified.
- `PUBLISHED`: Video is published and available for viewing.
- `UNPUBLISHED`: Video is unpublished, only admin can watch. ||
|| tusd | **[VideoTUSDSource](#yandex.cloud.video.v1.VideoTUSDSource)**

Upload video using the tus protocol.

Includes only one of the fields `tusd`.

Source type. ||
|| publicAccess | **[VideoPublicAccessRights](#yandex.cloud.video.v1.VideoPublicAccessRights)**

Video is available to everyone.

Includes only one of the fields `publicAccess`, `authSystemAccess`.

Video access rights. ||
|| authSystemAccess | **[VideoAuthSystemAccessRights](#yandex.cloud.video.v1.VideoAuthSystemAccessRights)**

Checking access rights using the authorization system.

Includes only one of the fields `publicAccess`, `authSystemAccess`.

Video access rights. ||
|| createdAt | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time when video was created. ||
|| updatedAt | **[google.protobuf.Timestamp](https://developers.google.com/protocol-buffers/docs/reference/google.protobuf#timestamp)**

Time of last video update. ||
|| labels | **string**

Custom labels as `` key:value `` pairs. Maximum 64 per resource. ||
|#

## VideoTUSDSource {#yandex.cloud.video.v1.VideoTUSDSource}

#|
||Field | Description ||
|| url | **string**

URL for uploading video via the tus protocol. ||
|#

## VideoPublicAccessRights {#yandex.cloud.video.v1.VideoPublicAccessRights}

#|
||Field | Description ||
|| Empty | > ||
|#

## VideoAuthSystemAccessRights {#yandex.cloud.video.v1.VideoAuthSystemAccessRights}

#|
||Field | Description ||
|| Empty | > ||
|#