# Reference
## Resources
<details><summary><code>client.resources.<a href="src/recoms/resources/client.py">counts</a>(...) -> ResourceCommentCountsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Render comment-count badges for an article or resource list without paginating roots and replies. Supply 1–100 distinct resourceId query values in request order; opaque IDs may contain slashes and must not be encoded as path segments. Returns exactly one entry per requested resource, including zeros. commentCount counts live comments with an APPROVED published version at every depth; rootCommentCount counts the subset with no parent. Deleted and never-published submissions are excluded, while pending/failed/rejected edits preserve counts of the previous published version. Counts reflect current committed state, not a stored counter; replyCount uses the same visibility but counts only a comment's direct replies. Project API key only; no author header. Read-only, available in frozen projects, consumes no write permits; no separate read rate limiter is applied.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.resources.counts(
    resource_ids=[
        "article:postgresql-cursors",
        "articles/nextjs"
    ],
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**resource_ids:** `typing.Optional[typing.Union[str, typing.Sequence[str]]]` — Required 1–100 distinct opaque resource IDs, in requested response order. Repeat resourceId in the query; do not comma-separate. IDs may contain slashes and must each contain 1–512 Unicode code points. Duplicates are invalid.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Comments
<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">list</a>(...) -> CommentListResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Public reads return approved content and deleted structural nodes only; unapproved revisions are never published. Authenticate from your backend with the project's API key, never from an end-user browser. Returns root comments only, not their replies. The required resourceId is your opaque resource identifier. Default sort is NEWEST; limit defaults to 20 (1–100). To continue, copy nextCursor into cursor with the same resourceId and sort. A null nextCursor ends the list. Replies have a separate listDirectReplies operation. Reads do not consume write permits.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.list(
    resource_id="article:postgresql-cursors",
    limit=20,
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**resource_id:** `str` — Opaque identifier assigned by your backend to the resource being discussed, such as an article or product. Use the same value for its entire discussion. Required, 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. Slashes are accepted; URL-encode it in query parameters. This is an identifier, not a resource URL.
    
</dd>
</dl>

<dl>
<dd>

**sort:** `typing.Optional[ListCommentsRequestSort]` — Root comment order. NEWEST: createdAt descending, id descending. OLDEST: createdAt ascending, id ascending. REACTIONS: reactionCount descending, then createdAt/id descending. REPLIES: direct replyCount descending, then createdAt/id descending. The ID tie-breaker makes pagination deterministic. Defaults to NEWEST when omitted.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum number of comments to return, from 1 to 100 inclusive. Defaults to 20 when omitted. A page may contain fewer items; nextCursor determines whether another page exists.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque continuation token returned as nextCursor by the same collection. Omit it for the first page. Pass it unchanged with the same project, collection, filters and sort. Do not decode, construct or reuse it for another query; incompatible tokens return INVALID_CURSOR. No fixed maximum token length is defined.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">create</a>(...) -> AuthorCommentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Authenticate the end user in your backend and pass their stable external author ID. The project key selects the tenant; it is not proof of end-user identity. Send JSON with Content-Type: application/json. Bodies must not exceed 16 KiB. Use Idempotency-Key when retrying an action after network failure. Successful AI moderation failure is not an HTTP error: inspect latestRevision.status. Omit parentId for a root comment, or supply the immediate parent to create a reply. A parent must be in the same project and resource and either have approved content or be a deleted node. Moderation may approve, reject, await review, or fail technically. The 201 response confirms the submission, not guaranteed publication. Read its latestRevision.status; only APPROVED content is public. New activity is blocked in frozen projects and when submission quota is unavailable.

Required API-key permission: comments:write. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.create(
    idempotency_key="0198a6ec-9e00-7000-8000-000000000010",
    resource_id="article:postgresql-cursors",
    author_user_id="usr_42",
    content="This explanation of **cursor pagination** was helpful.",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**resource_id:** `str` — Opaque identifier assigned by your backend to the resource being discussed, such as an article or product. Use the same value for its entire discussion. Required, 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. Slashes are accepted; URL-encode it in query parameters. This is an identifier, not a resource URL.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**content:** `str` — Markdown source, preserved without trimming. Must contain non-whitespace text and at most 2,000 Unicode code points. U+0000 and unpaired UTF-16 surrogates are forbidden. Render Markdown safely in your application's UI.
    
</dd>
</dl>

<dl>
<dd>

**idempotency_key:** `typing.Optional[str]` — Optional client-generated UUID for one mutation. A successful committed result is replayed for 24 hours for the same project, author, method, path, key and request. Reusing the key with different input returns 409 IDEMPOTENCY_KEY_REUSED. Failed requests do not reserve the key. Preserve this key when retrying after an uncertain network outcome.
    
</dd>
</dl>

<dl>
<dd>

**parent_id:** `typing.Optional[str]` — Optional ID of the immediate parent comment. Omit it to create a root comment; null is not accepted. The parent must be in the same project and resource, and have a published version or be a deleted node. Replies can be nested.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">get</a>(...) -> CommentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Public reads return approved content and deleted structural nodes only; unapproved revisions are never published. Authenticate from your backend with the project's API key, never from an end-user browser. Pending, rejected or failed submissions without a public version return 404. A pending edit does not hide the previous approved text. Deleted comments return DELETED with null content and retain their position in the thread. Use getAuthorComment to inspect an author's unpublished submission.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.get(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">delete</a>(...) -> DeleteCommentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

The externally authenticated author in X-Author-User-Id must own the comment. Soft deletion hides its source in public and author responses but preserves the structural node, reply relationships and stored revision history for authorized dashboard members. This is not author erasure. Deletion remains available in frozen projects and consumes write permits. A second deletion without replay returns 409 COMMENT_DELETED; use the original Idempotency-Key to retry the same action. No request body is required.

Required API-key permission: comments:write. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.delete(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    author_user_id="usr_42",
    idempotency_key="0198a6ec-9e00-7000-8000-000000000010",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**idempotency_key:** `typing.Optional[str]` — Optional client-generated UUID for one mutation. A successful committed result is replayed for 24 hours for the same project, author, method, path, key and request. Reusing the key with different input returns 409 IDEMPOTENCY_KEY_REUSED. Failed requests do not reserve the key. Preserve this key when retrying after an uncertain network outcome.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">update</a>(...) -> AuthorCommentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Authenticate the end user in your backend and pass their stable external author ID. The project key selects the tenant; it is not proof of end-user identity. Send JSON with Content-Type: application/json. Bodies must not exceed 16 KiB. Use Idempotency-Key when retrying an action after network failure. Successful AI moderation failure is not an HTTP error: inspect latestRevision.status. authorUserId must own the comment. The entire Markdown source is replaced by a new revision; resource and parent identity cannot be changed. Existing published content stays visible while an edit is PENDING, FAILED or REJECTED. An APPROVED edit replaces it. Frozen projects and unavailable submission quota block new edits. Deleted comments cannot be edited. Use getAuthorComment to follow moderation state.

Required API-key permission: comments:write. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.update(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    idempotency_key="0198a6ec-9e00-7000-8000-000000000010",
    author_user_id="usr_42",
    content="Updated explanation of **cursor pagination**.",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**content:** `str` — Markdown source, preserved without trimming. Must contain non-whitespace text and at most 2,000 Unicode code points. U+0000 and unpaired UTF-16 surrogates are forbidden. Render Markdown safely in your application's UI.
    
</dd>
</dl>

<dl>
<dd>

**idempotency_key:** `typing.Optional[str]` — Optional client-generated UUID for one mutation. A successful committed result is replayed for 24 hours for the same project, author, method, path, key and request. Reusing the key with different input returns 409 IDEMPOTENCY_KEY_REUSED. Failed requests do not reserve the key. Preserve this key when retrying after an uncertain network outcome.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.comments.<a href="src/recoms/comments/client.py">list_replies</a>(...) -> CommentListResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Public reads return approved content and deleted structural nodes only; unapproved revisions are never published. Authenticate from your backend with the project's API key, never from an end-user browser. Lists immediate children, not all descendants, ordered by createdAt ascending then id ascending. The parent must be publicly accessible, including deleted nodes. To traverse deeper, call this operation for each reply. Limit defaults to 20 (1–100). Copy nextCursor into cursor for this same parent and project; null ends the list. A cursor from root comments or another parent is invalid.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.comments.list_replies(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    limit=20,
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum number of comments to return, from 1 to 100 inclusive. Defaults to 20 when omitted. A page may contain fewer items; nextCursor determines whether another page exists.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque continuation token returned as nextCursor by the same collection. Omit it for the first page. Pass it unchanged with the same project, collection, filters and sort. Do not decode, construct or reuse it for another query; incompatible tokens return INVALID_CURSOR. No fixed maximum token length is defined.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Reactions
<details><summary><code>client.reactions.<a href="src/recoms/reactions/client.py">set</a>(...) -> ReactionResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Authenticate the end user in your backend and pass their external authorUserId. Only comments with approved public content accept reactions. Each user has at most one LIKE or DISLIKE per comment: setting the same type leaves it unchanged and setting the other replaces it. A pending edit does not disable reactions to existing public content. Frozen projects block new reaction activity. This mutation consumes write permits; optional Idempotency-Key provides replay for network retries. Send JSON; the maximum body is 16 KiB.

Required API-key permission: comments:write. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.reactions.set(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    idempotency_key="0198a6ec-9e00-7000-8000-000000000010",
    author_user_id="usr_42",
    type="LIKE",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**type:** `SetReactionRequestType` — LIKE expresses a positive reaction; DISLIKE expresses a negative reaction. Each external user has at most one reaction per comment; setting the other type replaces it.
    
</dd>
</dl>

<dl>
<dd>

**idempotency_key:** `typing.Optional[str]` — Optional client-generated UUID for one mutation. A successful committed result is replayed for 24 hours for the same project, author, method, path, key and request. Reusing the key with different input returns 409 IDEMPOTENCY_KEY_REUSED. Failed requests do not reserve the key. Preserve this key when retrying after an uncertain network outcome.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.reactions.<a href="src/recoms/reactions/client.py">remove</a>(...) -> ReactionResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Remove the reaction belonging to the external user in X-Author-User-Id. Your backend authenticates this identity. No request body is required. If no reaction exists, return type null and unchanged counters. Deleted comments and comments without approved public content cannot be targeted. Removal remains available in frozen projects and consumes write permits. Use the original Idempotency-Key to retry an uncertain action; other users' reactions are unchanged.

Required API-key permission: comments:write. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.reactions.remove(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    author_user_id="usr_42",
    idempotency_key="0198a6ec-9e00-7000-8000-000000000010",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**idempotency_key:** `typing.Optional[str]` — Optional client-generated UUID for one mutation. A successful committed result is replayed for 24 hours for the same project, author, method, path, key and request. Reusing the key with different input returns 409 IDEMPOTENCY_KEY_REUSED. Failed requests do not reserve the key. Preserve this key when retrying after an uncertain network outcome.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Project
<details><summary><code>client.project.<a href="src/recoms/project/client.py">get</a>() -> GetProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read project settings, billing state and non-secret provider configuration. Requires project:read. The key selects the project and its configured permissions remain authoritative. Moderation rules and the recommended reset policy are included for management access. Available while frozen; no write permits are consumed.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.get()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">update</a>(...) -> UpdateProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Atomically replace the supplied moderation mode, policy or analytics timezone. Requires project:update. Omitted fields remain unchanged; supply at least one. OFF publishes immediately, MANUAL queues review, AI resolves automatically, HYBRID queues suspected violations for review. Rules are trimmed. A timezone changes analytics calendar boundaries, not stored UTC timestamps. Existing decisions are not rerun. Refresh settings before retrying an uncertain outcome; Idempotency-Key replay is not supported. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.update(
    moderation_mode="HYBRID",
    analytics_timezone="Europe/Moscow",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**moderation_mode:** `typing.Optional[ModerationMode]` 
    
</dd>
</dl>

<dl>
<dd>

**moderation_policy:** `typing.Optional[str]` — Non-empty policy replacing the current policy after trimming; omit to preserve it.
    
</dd>
</dl>

<dl>
<dd>

**analytics_timezone:** `typing.Optional[str]` — Valid IANA timezone used for calendar analytics; omit to preserve it.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">analytics</a>(...) -> AnalyticsProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read discussion and moderation metrics in the project's timezone. Requires analytics:read. Discussion counts first publication rather than edits; moderation summary counts unique comments with approval/rejection decisions. CUSTOM requires inclusive YYYY-MM-DD dates; the returned endAt is exclusive and current-day ranges stop at request time. Free history is limited to 90 days; unavailable segmentation metrics are null. planUsage follows the current billing period independently of the selected range. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.analytics()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**preset:** `typing.Optional[AnalyticsRangePreset]` — Calendar preset in the project's analytics timezone; CUSTOM requires both custom dates. Available history follows project entitlements.
    
</dd>
</dl>

<dl>
<dd>

**custom_start_date:** `typing.Optional[datetime.date]` — Inclusive calendar start date, YYYY-MM-DD, in the project's timezone; required for CUSTOM.
    
</dd>
</dl>

<dl>
<dd>

**custom_end_date:** `typing.Optional[datetime.date]` — Inclusive calendar end date, YYYY-MM-DD, in the project's timezone; required for CUSTOM. The response endAt is the next day's exclusive boundary, or the request time when the current date is selected. Dates must be ordered and not in the future.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">usage</a>() -> UsageProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read submission usage and allowance for the current project billing period. Requires usage:read. Edits count as submissions; freeze/activation does not reset usage. Returns null without billing state. With billing state but no current usage interval, consumed is zero, included follows the plan and period is null. This read never reserves quota and is available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.usage()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">freeze</a>(...) -> FreezeProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Freeze an ACTIVE Free project and release its owner's active Free slot. Requires verified owner access; project-only API keys do not grant this authority. Comments, revisions, reactions and memberships are retained. Reads and removals remain available; new submissions, edits and reaction additions are blocked. Usage is not reset. Activation can restore access subject to Free allocation and membership requirements. Repeating after success returns PROJECT_STATE_CONFLICT; refresh before retrying an uncertain result. Send an empty JSON object.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.freeze()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `EmptyProjectAction` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">activate</a>(...) -> ActivateProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Activate a FROZEN Free project. Requires verified owner access; project-only API keys do not grant this authority. At most two owned Free projects may be ACTIVE. If both slots are occupied, supply another owned ACTIVE Free project's freezeProjectId. Non-owner memberships require confirmMemberRemoval=true and are removed before activation; pending invitations are canceled. Free allocation changes are atomic, but membership removal can precede a failed activation: ACTIVATION_PARTIALLY_COMPLETED requires refreshing billing and members before another attempt. Existing comments and usage are retained. Idempotency-Key replay is not supported.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.activate(
    confirm_member_removal=False,
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**freeze_project_id:** `typing.Optional[str]` — Another ACTIVE Free project owned by the authenticated owner to freeze when both Free slots are occupied. Omit when a slot is available; this cannot be the project being activated.
    
</dd>
</dl>

<dl>
<dd>

**confirm_member_removal:** `typing.Optional[bool]` — Explicit consent to remove every non-owner membership before Free activation. Required when participants exist; pending invitations are canceled during activation even without participants.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.<a href="src/recoms/project/client.py">transfer_ownership</a>(...) -> TransferOwnershipProjectResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Transfer the project to another existing member. Requires verified owner access; project-only API keys do not grant this authority. targetUserId identifies the target RECOMS account, not an external comment author or membership. The target becomes the sole owner and the previous owner becomes admin atomically. Existing project data remains. Obtain explicit consent before changing ownership. Missing target membership returns VALIDATION_ERROR; changed ownership returns FORBIDDEN. Refresh membership after an uncertain outcome instead of automatically retrying.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.transfer_ownership(
    target_user_id="0198a6ec-9e00-7000-8000-000000000002",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**target_user_id:** `str` — RECOMS account ID of another existing project member, not a membership ID or external comment author ID. The current owner becomes admin.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Dashboard
<details><summary><code>client.dashboard.<a href="src/recoms/dashboard/client.py">projects</a>() -> ProjectsDashboardResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

List projects accessible to the authenticated RECOMS account, ordered by membership creation time. Project-only API keys do not grant account-wide access. Returns membership identity and role with each project; organizationId identifies the project, while id identifies the membership. Moderation rules are redacted to empty strings for roles other than owner/admin. Credentials are never returned. An account with no memberships receives an empty data array.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.dashboard.projects()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.dashboard.<a href="src/recoms/dashboard/client.py">config</a>() -> ConfigDashboardResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read the availability of Google and GitHub sign-in for this deployment. This anonymous read returns only boolean availability, never provider credentials, account data or configuration secrets. It does not change state.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.dashboard.config()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Me Reactions
<details><summary><code>client.me.reactions.<a href="src/recoms/me/reactions/client.py">list</a>(...) -> AuthorReactionListResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Restore liked/disliked UI state after a reload without writing or persisting reaction state in your own database. Your backend authenticates the user before supplying X-Author-User-Id. Returns only that user's existing reactions on APPROVED, non-deleted comments in the key's project and required resource; pending edits preserve existing public visibility. Optionally repeat commentId (maximum 100) to restrict to rendered comments. Ordered by reaction creation time descending, then reaction ID descending. limit defaults to 20 (1–100). Continue using nextCursor with the same project, author, resource and commentId filter set; null ends the list. Read-only, available in frozen projects, consumes no write permits; no separate read rate limiter is applied.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.me.reactions.list(
    resource_id="article:postgresql-cursors",
    comment_ids=[
        "0198a6ec-9e00-7000-8000-000000000001"
    ],
    limit=20,
    author_user_id="usr_42",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**resource_id:** `str` — Opaque identifier assigned by your backend to the resource being discussed, such as an article or product. Use the same value for its entire discussion. Required, 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. Slashes are accepted; URL-encode it in query parameters. This is an identifier, not a resource URL.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**comment_ids:** `typing.Optional[typing.Union[str, typing.Sequence[str]]]` — Optional rendered comment IDs. Repeat commentId in the query (not comma-separated); at most 100 values. Omit to read all of the author's public reactions in this resource. Comments without a reaction are absent.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum reactions per page, 1–100 inclusive; defaults to 20. Use nextCursor to continue.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque continuation token returned as nextCursor by the same collection. Omit it for the first page. Pass it unchanged with the same project, collection, filters and sort. Do not decode, construct or reuse it for another query; incompatible tokens return INVALID_CURSOR. No fixed maximum token length is defined.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Me Comments
<details><summary><code>client.me.comments.<a href="src/recoms/me/comments/client.py">list</a>(...) -> AuthorCommentListResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Build a my-comments page or recover submissions whose IDs were lost, including comments awaiting review, without duplicating comment state in your database. Your backend authenticates the author supplied in X-Author-User-Id; the project key and author scope every result. Roots and replies are included, deleted comments are excluded. Optional resourceId narrows the resource; repeat latestRevisionStatus to match the latest submission only, including pending edits of already published comments. Returns the existing AuthorComment representation: published content plus latestRevision. NEWEST (default) orders comment creation time and ID descending; OLDEST orders both ascending. limit defaults to 20 (1–100). Copy nextCursor with unchanged project, author, filters and sort; null ends the list. Filter order is immaterial and limit may change; pagination is not a snapshot across requests. Read-only, available in frozen projects, consumes no write permits; no separate read rate limiter is applied.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.me.comments.list(
    resource_id="article:postgresql-cursors",
    limit=20,
    author_user_id="usr_42",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**resource_id:** `typing.Optional[str]` — Optional opaque resource filter; omit to list across this project. May contain slashes. Accepts 1–512 Unicode code points.
    
</dd>
</dl>

<dl>
<dd>

**latest_revision_statuses:** `typing.Optional[typing.Union[ListCommentsRequestLatestRevisionStatusItem, typing.Sequence[ListCommentsRequestLatestRevisionStatusItem]]]` — Optional latest-submission states, passed as repeated query parameters. PENDING awaits review, FAILED needs review after technical failure, REJECTED was declined, APPROVED was published. Filters the latest revision only, so pending edits of approved comments are included. Omit to include every live submission state.
    
</dd>
</dl>

<dl>
<dd>

**sort:** `typing.Optional[ListCommentsRequestSort]` — NEWEST (default): creation time descending, then ID descending. OLDEST: creation time ascending, then ID ascending. Edits do not change creation time.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum number of comments to return, from 1 to 100 inclusive. Defaults to 20 when omitted. A page may contain fewer items; nextCursor determines whether another page exists.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque continuation token returned as nextCursor by the same collection. Omit it for the first page. Pass it unchanged with the same project, collection, filters and sort. Do not decode, construct or reuse it for another query; incompatible tokens return INVALID_CURSOR. No fixed maximum token length is defined.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.me.comments.<a href="src/recoms/me/comments/client.py">get</a>(...) -> AuthorCommentResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read a comment owned by the externally authenticated user identified by X-Author-User-Id. Your backend must establish this identity; never trust an arbitrary client-supplied user ID. The response contains published content and the latest submitted revision, including PENDING, FAILED or REJECTED. A published comment can remain APPROVED while its edit awaits review. Deleted source is hidden in both content fields. This read does not consume write permits.

Required API-key permission: comments:read. A valid key without this permission is not sufficient; author identity and ownership requirements still apply where documented.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.me.comments.get(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    author_user_id="usr_42",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUIDv7 identifying a comment in the authenticated project. Copy the identifier from a response; UUID input syntax is validated.
    
</dd>
</dl>

<dl>
<dd>

**author_user_id:** `str` — Stable external user identifier assigned by your backend after authenticating the end user. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden. This is not a RECOMS dashboard account ID or a display name.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Project Comments
<details><summary><code>client.project.comments.<a href="src/recoms/project/comments/client.py">queues</a>() -> QueuesCommentsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Count the latest PENDING and FAILED revisions of live comments, including pending edits of already published comments. Requires comments:read. Deleted comments are excluded. FAILED is a technical check failure, not rejection. These counts and a separate list are not a shared snapshot. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.comments.queues()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.comments.<a href="src/recoms/project/comments/client.py">list</a>(...) -> DashboardComments</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

List roots and replies with their latest moderation revision. Requires comments:read. Filter status follows the latest revision, so an approved comment with a pending edit appears under PENDING. Creation time or current reaction/reply counts determine order; ID breaks ties. Continue with nextCursor and identical filters/sort; null ends pagination. Pages are not a snapshot. hasComments ignores filters; queues describes moderation backlog. Available while frozen; no write permits are consumed.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.comments.list()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**search:** `typing.Optional[str]` — Text search in comment content; at most 512 Unicode code points. Omit or use an empty string for no text filter.
    
</dd>
</dl>

<dl>
<dd>

**status:** `typing.Optional[DashboardCommentStatus]` — Filter by latest revision decision or deleted node. ALL and unpublished states require moderation access.
    
</dd>
</dl>

<dl>
<dd>

**sort:** `typing.Optional[DashboardCommentSort]` — Creation time descending or ascending, total reactions descending, or published direct reply count descending; identifier breaks ties.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum rows in this page, inclusive range 1–100.
    
</dd>
</dl>

<dl>
<dd>

**cursor:** `typing.Optional[str]` — Opaque nextCursor from the preceding page. Keep filters and sort unchanged; incompatible tokens return INVALID_CURSOR. Omit to restart. Pagination is not a snapshot.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.comments.<a href="src/recoms/project/comments/client.py">review</a>(...) -> ReviewCommentsResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Approve or reject the specified latest PENDING or FAILED revision. Requires comments:review. Approval publishes that revision; rejection retains an earlier approved version. A key decision has no verified moderator-account attribution. A stale or already decided revision returns REVISION_ALREADY_REVIEWED. Refresh before another action or after an uncertain network outcome; this operation does not replay Idempotency-Key. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.comments.review(
    comment_id="0198a6ec-9e00-7000-8000-000000000001",
    revision_id="0198a6ec-9e00-7000-8000-000000000003",
    decision="APPROVED",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**comment_id:** `str` — RECOMS-generated UUID. Copy it from the corresponding project, member, comment or revision response; identifiers are not interchangeable.
    
</dd>
</dl>

<dl>
<dd>

**revision_id:** `str` — Latest PENDING or FAILED revision being reviewed; refresh if another decision has already been recorded. Copy the revision ID, not the comment ID.
    
</dd>
</dl>

<dl>
<dd>

**decision:** `ReviewCommentDecision` — Publish this revision or reject it while retaining any previously approved public version.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Project Moderation
<details><summary><code>client.project.moderation.<a href="src/recoms/project/moderation/client.py">test</a>(...) -> TestModerationResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Test draft rules against example text using the current provider. Requires provider:test. No policy or comment is saved; no submission quota is consumed. The provider attempt is recorded and the separate provider-test rate limit applies. ALLOWED and DISALLOWED are successful test outcomes. Provider absence or technical failure returns PROVIDER_UNAVAILABLE rather than a content rejection. RATE_LIMITED includes Retry-After. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.moderation.test(
    moderation_policy="Reject unsolicited advertising; allow relevant discussion.",
    example_comment="Thank you for this explanation.",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**moderation_policy:** `str` — Draft moderation policy used only for this test; it is not saved.
    
</dd>
</dl>

<dl>
<dd>

**example_comment:** `str` — Markdown source, preserved without trimming. Must contain non-whitespace text and at most 2,000 Unicode code points. U+0000 and unpaired UTF-16 surrogates are forbidden. Render Markdown safely in your application's UI.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

## Project Provider
<details><summary><code>client.project.provider.<a href="src/recoms/project/provider/client.py">configure</a>(...) -> ConfigureProviderResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Test and save an OpenAI-compatible HTTPS provider. Requires provider:configure. Unsafe outbound destinations are blocked. Settings are saved only after a successful connection test; a failed candidate does not replace the current configuration. preserve retains an existing credential or accepts an initial one; replace requires credential; remove clears authentication. Credentials are encrypted and never returned. Refresh after PROVIDER_CONFIGURATION_CHANGED; wait Retry-After on RATE_LIMITED. Provider tests do not consume submission quota. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.provider.configure(
    base_url="https://api.openai.com/v1",
    model="gpt-4.1-mini",
    credential="example-provider-key-not-a-secret",
    credential_intent="replace",
)

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**base_url:** `str` — OpenAI-compatible HTTPS API base URL, 1–2,048 Unicode code points after trimming; for example https://api.openai.com/v1. Do not embed a username or password. Private, loopback and otherwise unsafe destinations are blocked by outbound safety checks, including DNS resolution and redirects. A valid URL is not proof that the provider can be reached.
    
</dd>
</dl>

<dl>
<dd>

**model:** `str` — Provider model identifier, 1–256 Unicode code points after trimming; for example gpt-4.1-mini.
    
</dd>
</dl>

<dl>
<dd>

**credential:** `typing.Optional[str]` — Provider API credential, at most 4,096 Unicode code points. Required and non-blank for replace intent. With preserve, an existing credential is retained; on first configuration this value may supply the initial credential. With remove it is ignored. It is never echoed in responses.
    
</dd>
</dl>

<dl>
<dd>

**credential_intent:** `typing.Optional[ConfigureProviderCredentialIntent]` — Preserve the current credential, replace it with credential, or remove it for a provider without authentication.
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

<details><summary><code>client.project.provider.<a href="src/recoms/project/provider/client.py">test</a>(...) -> TestProviderResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retry the saved provider connection. Requires provider:test. Availability and last test outcome may change and the attempt is recorded, but settings are not replaced. Missing/unreadable credentials or an unusable provider return PROVIDER_UNAVAILABLE; concurrent changes return PROVIDER_CONFIGURATION_CHANGED. Respect Retry-After on RATE_LIMITED. This diagnostic call changes test state, consumes provider-test capacity and does not consume comment submission quota. Available while frozen.
</dd>
</dl>
</dd>
</dl>

#### 🔌 Usage

<dl>
<dd>

<dl>
<dd>

```python
from recoms import RecomsClient
from recoms.environment import RecomsClientEnvironment

client = RecomsClient(
    token="<token>",
    environment=RecomsClientEnvironment.DEFAULT,
)

client.project.provider.test()

```
</dd>
</dl>
</dd>
</dl>

#### ⚙️ Parameters

<dl>
<dd>

<dl>
<dd>

**request:** `EmptyProjectAction` 
    
</dd>
</dl>

<dl>
<dd>

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

