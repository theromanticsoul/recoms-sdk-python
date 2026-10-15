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

**author_user_id:** `str` — Stable user identifier from your application, supplied by your backend after authenticating the end user. Use your application's user ID, not a RECOMS account ID or a display name. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden.
    
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

The externally authenticated author in X-Author-User-Id must own the comment. Soft deletion hides its source in public and author responses but preserves the structural node, reply relationships and stored revision history. This is not author erasure. Deletion remains available in frozen projects and consumes write permits. A second deletion without replay returns 409 COMMENT_DELETED; use the original Idempotency-Key to retry the same action. No request body is required.

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

**author_user_id:** `str` — UTF-8 percent-encoded author ID from your application. Encode once with JavaScript encodeURIComponent(id) or Python urllib.parse.quote(id, safe="") before supplying this HTTP header or SDK header argument. usr_42 stays usr_42; a literal % becomes %25. The API decodes exactly once, then validates the original 1–512 Unicode code point identifier and checks ownership. Plus is not a space escape. Invalid escapes, UTF-8, null characters or decoded identifiers return VALIDATION_ERROR. Maximum encoded length is 6,144 ASCII characters. JSON authorUserId fields and response IDs are not encoded.
    
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

**author_user_id:** `str` — Stable user identifier from your application, supplied by your backend after authenticating the end user. Use your application's user ID, not a RECOMS account ID or a display name. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden.
    
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

**author_user_id:** `str` — Stable user identifier from your application, supplied by your backend after authenticating the end user. Use your application's user ID, not a RECOMS account ID or a display name. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden.
    
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

**author_user_id:** `str` — UTF-8 percent-encoded author ID from your application. Encode once with JavaScript encodeURIComponent(id) or Python urllib.parse.quote(id, safe="") before supplying this HTTP header or SDK header argument. usr_42 stays usr_42; a literal % becomes %25. The API decodes exactly once, then validates the original 1–512 Unicode code point identifier and checks ownership. Plus is not a space escape. Invalid escapes, UTF-8, null characters or decoded identifiers return VALIDATION_ERROR. Maximum encoded length is 6,144 ASCII characters. JSON authorUserId fields and response IDs are not encoded.
    
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

## Users
<details><summary><code>client.users.<a href="src/recoms/users/client.py">list</a>(...) -> ListUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Discover users without persisting every RECOMS ID in your database. Search external identities/names and inspect current profile, comment counts and effective blocks. Deleting users are excluded immediately. Order is createdAt plus ID, without snapshot guarantees. Continue with nextCursor and identical filters/sort; null ends pagination. Block expiration may change membership between pages.

Requires users:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.list()

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

**search:** `typing.Optional[str]` — Case-insensitive substring of external authorUserId or displayName; omit for all users.
    
</dd>
</dl>

<dl>
<dd>

**blocked:** `typing.Optional[ListUsersRequestBlocked]` — Filter effective block state at request time; expired blocks are unblocked.
    
</dd>
</dl>

<dl>
<dd>

**sort:** `typing.Optional[ListUsersRequestSort]` — Order by creation time with ID tie-breaker. Defaults to NEWEST; changes require restarting pagination.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum users per page, 1–100, default 20. Read nextCursor to determine completion.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">create</a>(...) -> CreateUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Optionally register an application's user before their first comment or reaction, or before setting a block. The first persisted comment or reaction also creates a user automatically, in the same transaction; failed submissions do not leave a user behind. Reactions remain available to blocked users. The external authorUserId is unique within this project and immutable. Duplicate creation never replaces a profile: use lookup and update instead. Omitted profile fields become null. No login, membership, comment or quota consumption is created.

Requires users:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.create(
    author_user_id="customer/42",
    display_name="Alex",
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

**author_user_id:** `str` — Stable user identifier from your application, supplied by your backend after authenticating the end user. Use your application's user ID, not a RECOMS account ID or a display name. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden.
    
</dd>
</dl>

<dl>
<dd>

**display_name:** `typing.Optional[str]` — Optional public display name, 1–128 characters after trimming. Null clears it. Treat as plain text, never HTML.
    
</dd>
</dl>

<dl>
<dd>

**avatar_url:** `typing.Optional[str]` — Optional absolute HTTPS avatar URL, at most 2048 characters, without credentials. Null clears it. RECOMS stores the URL, not the image; your UI chooses how to load external images.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">lookup</a>(...) -> LookupUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Resolve your authorUserId to the internal ID used in user paths. Send the identifier as a URL-encoded query value so slashes and Unicode do not change path routing. This read never creates a user. Missing or deleting users return NOT_FOUND; another project's records are never searched.

Requires users:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.lookup(
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

**author_user_id:** `str` — Stable user identifier from your application, supplied by your backend after authenticating the end user. Use your application's user ID, not a RECOMS account ID or a display name. Required for every author; anonymous authors are not supported. Accepts 1–512 Unicode code points; U+0000 and unpaired UTF-16 surrogates are forbidden.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">get</a>(...) -> GetUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read the optional public profile plus management-only block, counts and latest comment time. Use a RECOMS user ID, not an external authorUserId or account ID. Block notes are not included in public comments. Deleting users return NOT_FOUND.

Requires users:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.get(
    user_id="0198a6ec-9e00-7000-8000-000000000001",
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

**user_id:** `str` — Internal RECOMS user ID returned by create/list/lookup, not authorUserId.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">delete</a>(...) -> DeleteUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Accept asynchronous erasure of profile, authored text and all revisions/reasons, reactions, block, idempotency snapshots and personal webhook payloads. Foreign replies survive with anonymous deleted structural parents. Data becomes inaccessible and new writes stop immediately; 202 is acceptance, not completion. Save the receipt ID and poll getUserDeletion using users:delete. Repeating while the user record exists returns the same receipt. After completion, the former user ID returns NOT_FOUND. A later submission with the same external authorUserId can create a new unrelated user. Backups and already delivered external data follow separate retention/erasure procedures; online completion does not promise their immediate physical removal.

Requires users:delete. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.delete(
    user_id="0198a6ec-9e00-7000-8000-000000000001",
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

**user_id:** `str` — Internal RECOMS user ID returned by create/list/lookup, not authorUserId.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">update</a>(...) -> UpdateUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Change displayName and/or avatarUrl. Omitted fields are preserved and null clears a field. At least one field is required; unknown fields are rejected. The profile is shared by the author's comments, so subsequent reads reflect this change without editing or remoderating comments. External identity is immutable. With users:read, call getUser after an uncertain outcome; otherwise retry only the identical intended replacement.

Requires users:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.update(
    user_id="0198a6ec-9e00-7000-8000-000000000001",
    display_name="Alex",
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

**user_id:** `str` — Internal RECOMS user ID returned by create/list/lookup, not authorUserId.
    
</dd>
</dl>

<dl>
<dd>

**display_name:** `typing.Optional[str]` — Optional public display name, 1–128 characters after trimming. Null clears it. Treat as plain text, never HTML.
    
</dd>
</dl>

<dl>
<dd>

**avatar_url:** `typing.Optional[str]` — Optional absolute HTTPS avatar URL, at most 2048 characters, without credentials. Null clears it. RECOMS stores the URL, not the image; your UI chooses how to load external images.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">block</a>(...) -> BlockUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Set or replace a project-wide temporary or permanent block. expiresAt must be in the future or null for indefinite duration. Prevents roots, replies and edits before moderation/quota consumption. Reading, reactions and deletion of own comments remain possible. Existing published comments and review queues are preserved. The verified caller is attributed automatically; internal reason is never shown to the author. Expiration is evaluated at request time; no unblock job is necessary. Repeating the same intended block is safe.

Requires users:block. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.block(
    user_id="0198a6ec-9e00-7000-8000-000000000001",
    expires_at=None,
    reason="Repeated unsolicited advertising",
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

**user_id:** `str` — Internal RECOMS user ID returned by create/list/lookup, not authorUserId.
    
</dd>
</dl>

<dl>
<dd>

**expires_at:** `typing.Optional[datetime.datetime]` — Future RFC3339 expiration with timezone, or null for an indefinite block. A past date is rejected.
    
</dd>
</dl>

<dl>
<dd>

**reason:** `typing.Optional[str]` — Optional internal reason, 1–1000 characters after trimming, or null to clear.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">unblock</a>(...) -> UnblockUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Clear any temporary/permanent block and its internal note. Repeating for an existing unblocked user succeeds. Does not publish pending comments or restore deleted data. Deleting users cannot be unblocked to bypass erasure.

Requires users:block. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.unblock(
    user_id="0198a6ec-9e00-7000-8000-000000000001",
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

**user_id:** `str` — Internal RECOMS user ID returned by create/list/lookup, not authorUserId.
    
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

<details><summary><code>client.users.<a href="src/recoms/users/client.py">get_deletion</a>(...) -> GetDeletionUsersResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read the non-personal receipt saved by deleteUser. PENDING/PROCESSING are unfinished; COMPLETED means online erasure finished. FAILED keeps data inaccessible and requires resuming the same deletion. Contains no erased profile or external ID. Requires users:delete, so recovery does not require an additional users:read grant.

Requires users:delete. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.users.get_deletion(
    deletion_id="0198a6ec-9e00-7000-8000-000000000001",
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

**deletion_id:** `str` — Deletion operation ID from the accepted deleteUser response.
    
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

## Webhooks
<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">list</a>(...) -> ListWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Read endpoint URLs, explicit subscriptions and enabled states without exposing secrets. Ordered newest first by createdAt then ID. Continue using nextCursor; null ends the list. No snapshot is guaranteed between pages.

Requires webhooks:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.list()

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

**limit:** `typing.Optional[int]` — Maximum items per page, 1–100, default 20.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">create</a>(...) -> CreateWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Create a project-scoped HTTPS endpoint with explicit event subscriptions. Returns the signing secret exactly once: store it securely on the receiver. A secret is not an API key. New event types are not automatically subscribed. URL validation never follows redirects or allows private addresses. After an uncertain create, listing endpoints requires webhooks:read; do not blindly repeat and create duplicates. Events are committed with the domain change and delivered asynchronously. Accept only after verifying Standard Webhooks HMAC-SHA256 against the exact raw body, event ID and attempt timestamp. Return any 2xx to acknowledge. Attempts time out after 10 seconds; after failures, retries wait 1 minute, 5 minutes, 30 minutes, 2 hours, 8 hours and 24 hours from the preceding failure. Duplicates and out-of-order arrival are possible; deduplicate by stable event.id. History/payload retention is 30 days and may end earlier for user erasure. No comment quota is consumed and a receiver failure never rolls back a comment.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.create(
    url="https://example.com/webhooks/recoms",
    event_types=[
        "comment.published"
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

**url:** `str` — Absolute public HTTPS receiver URL, at most 2048 characters, without credentials or fragments. Private, loopback and metadata destinations are forbidden; redirects are not followed.
    
</dd>
</dl>

<dl>
<dd>

**event_types:** `typing.List[WebhookEventType]` — Distinct explicitly subscribed event types. At least one is required; new types are never subscribed automatically.
    
</dd>
</dl>

<dl>
<dd>

**enabled:** `typing.Optional[bool]` — Whether to accept new deliveries. Defaults to true; disabling pauses queued delivery.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">get</a>(...) -> GetWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Inspect an endpoint from this project. Signing secrets cannot be recovered by GET; explicitly rotate if a secret was lost. Events are committed with the domain change and delivered asynchronously. Accept only after verifying Standard Webhooks HMAC-SHA256 against the exact raw body, event ID and attempt timestamp. Return any 2xx to acknowledge. Attempts time out after 10 seconds; after failures, retries wait 1 minute, 5 minutes, 30 minutes, 2 hours, 8 hours and 24 hours from the preceding failure. Duplicates and out-of-order arrival are possible; deduplicate by stable event.id. History/payload retention is 30 days and may end earlier for user erasure. No comment quota is consumed and a receiver failure never rolls back a comment.

Requires webhooks:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.get(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">delete</a>(...) -> DeleteWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Remove this endpoint, signing secret and delivery history, and stop its queued attempts. It does not delete comments or other endpoints. An already accepted receiver request cannot be recalled. Subsequent reads return NOT_FOUND. With webhooks:read, inspect after an uncertain result; do not interpret an absent endpoint as failed deletion.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.delete(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">update</a>(...) -> UpdateWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Replace supplied URL, subscriptions or enabled state; omitted fields remain unchanged. At least one field is required. URL changes apply to future attempts, including queued deliveries; changing subscriptions affects new events only. Disabling pauses queued deliveries until re-enabled or retention expires. Does not rotate the secret. Read back with webhooks:read after uncertain completion; otherwise repeat only the identical intended update.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.update(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
    enabled=False,
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
</dd>
</dl>

<dl>
<dd>

**url:** `typing.Optional[str]` — Absolute public HTTPS receiver URL, at most 2048 characters, without credentials or fragments. Private, loopback and metadata destinations are forbidden; redirects are not followed.
    
</dd>
</dl>

<dl>
<dd>

**event_types:** `typing.Optional[typing.List[WebhookEventType]]` — Distinct explicitly subscribed event types. At least one is required; new types are never subscribed automatically.
    
</dd>
</dl>

<dl>
<dd>

**enabled:** `typing.Optional[bool]` — Change enabled state only when supplied; omission never re-enables a paused endpoint.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">rotate_secret</a>(...) -> RotateSecretWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Invalidate the old signing secret for future attempts and return a new secret exactly once. Queued retries use the new secret. Update your receiver after rotation. An already in-flight attempt may have used the previous secret. Idempotency-Key replay is not supported; never automatically repeat after an uncertain result because another rotation invalidates the preceding key.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.rotate_secret(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">test</a>(...) -> TestWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Queue a synthetic comment.revision.created payload with data.test=true, regardless of subscribed types, to check your receiver. No actual user, comment or revision is created. A disabled endpoint remains paused. The response means queued, not delivered; read delivery details with webhooks:read to inspect acknowledgment. Repeating creates another test. Events are committed with the domain change and delivered asynchronously. Accept only after verifying Standard Webhooks HMAC-SHA256 against the exact raw body, event ID and attempt timestamp. Return any 2xx to acknowledge. Attempts time out after 10 seconds; after failures, retries wait 1 minute, 5 minutes, 30 minutes, 2 hours, 8 hours and 24 hours from the preceding failure. Duplicates and out-of-order arrival are possible; deduplicate by stable event.id. History/payload retention is 30 days and may end earlier for user erasure. No comment quota is consumed and a receiver failure never rolls back a comment.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.test(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">list_deliveries</a>(...) -> ListDeliveriesWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Newest-first delivery history for this endpoint, ordered by createdAt and ID, within retention. Erased users' events are excluded. Failed and queued deliveries are visible, not just successful ones. Keep the endpoint unchanged while continuing with nextCursor. Events are committed with the domain change and delivered asynchronously. Accept only after verifying Standard Webhooks HMAC-SHA256 against the exact raw body, event ID and attempt timestamp. Return any 2xx to acknowledge. Attempts time out after 10 seconds; after failures, retries wait 1 minute, 5 minutes, 30 minutes, 2 hours, 8 hours and 24 hours from the preceding failure. Duplicates and out-of-order arrival are possible; deduplicate by stable event.id. History/payload retention is 30 days and may end earlier for user erasure. No comment quota is consumed and a receiver failure never rolls back a comment.

Requires webhooks:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.list_deliveries(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
</dd>
</dl>

<dl>
<dd>

**limit:** `typing.Optional[int]` — Maximum items per page, 1–100, default 20.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">get_delivery</a>(...) -> GetDeliveryWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Inspect the immutable event, scheduled retry and safe attempt results. Receiver bodies, secrets and raw exceptions are not stored in this response. Missing, expired, erased and out-of-project IDs all return NOT_FOUND. Events are committed with the domain change and delivered asynchronously. Accept only after verifying Standard Webhooks HMAC-SHA256 against the exact raw body, event ID and attempt timestamp. Return any 2xx to acknowledge. Attempts time out after 10 seconds; after failures, retries wait 1 minute, 5 minutes, 30 minutes, 2 hours, 8 hours and 24 hours from the preceding failure. Duplicates and out-of-order arrival are possible; deduplicate by stable event.id. History/payload retention is 30 days and may end earlier for user erasure. No comment quota is consumed and a receiver failure never rolls back a comment.

Requires webhooks:read. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.get_delivery(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
    delivery_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
</dd>
</dl>

<dl>
<dd>

**delivery_id:** `str` — Delivery ID belonging to this endpoint and project.
    
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

<details><summary><code>client.webhooks.<a href="src/recoms/webhooks/client.py">replay_delivery</a>(...) -> ReplayDeliveryWebhooksResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Schedule the same event ID and payload for another attempt now. Receiver deduplication still applies. Reject a currently processing attempt with WEBHOOK_DELIVERY_IN_PROGRESS; inspect it before retrying. Erased/expired events cannot be replayed. This does not change comments or create a new domain event. Reading status requires webhooks:read. Do not blindly repeat after an uncertain result.

Requires webhooks:write. Available in frozen projects. Does not consume comment write permits or submission quota. Success uses a data envelope and all responses include X-Request-Id.
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

client.webhooks.replay_delivery(
    webhook_id="0198a6ec-9e00-7000-8000-000000000001",
    delivery_id="0198a6ec-9e00-7000-8000-000000000001",
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

**webhook_id:** `str` — Webhook endpoint ID in this project.
    
</dd>
</dl>

<dl>
<dd>

**delivery_id:** `str` — Delivery ID belonging to this endpoint and project.
    
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

Read project settings, billing state and non-secret provider configuration. Requires project:read. The key selects the project. access returns its keyId and effective permissions in resource:action form, allowing you to choose available operations before sending a request. Permissions can change; the API checks each request independently. Moderation rules and the recommended reset policy are included for management access. Available while frozen; no write permits are consumed.
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

Atomically replace the supplied moderation mode, policy or analytics timezone. Requires project:update. Omitted fields remain unchanged; supply at least one. OFF publishes immediately, MANUAL queues review, AI resolves automatically, HYBRID queues suspected violations for review. Rules are trimmed. A timezone changes analytics calendar boundaries, not stored UTC timestamps. Existing decisions are not rerun. After an uncertain outcome, call getProjectContext with project:read and compare the supplied fields with current settings before deciding whether another update is needed. project:update does not imply project:read; without that read grant, stop and report the unknown outcome for an appropriately scoped caller to verify. A read is not a concurrency lock; do not overwrite intervening changes automatically. Idempotency-Key replay is not supported. Available while frozen.
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

**author_user_id:** `str` — UTF-8 percent-encoded author ID from your application. Encode once with JavaScript encodeURIComponent(id) or Python urllib.parse.quote(id, safe="") before supplying this HTTP header or SDK header argument. usr_42 stays usr_42; a literal % becomes %25. The API decodes exactly once, then validates the original 1–512 Unicode code point identifier and checks ownership. Plus is not a space escape. Invalid escapes, UTF-8, null characters or decoded identifiers return VALIDATION_ERROR. Maximum encoded length is 6,144 ASCII characters. JSON authorUserId fields and response IDs are not encoded.
    
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

**author_user_id:** `str` — UTF-8 percent-encoded author ID from your application. Encode once with JavaScript encodeURIComponent(id) or Python urllib.parse.quote(id, safe="") before supplying this HTTP header or SDK header argument. usr_42 stays usr_42; a literal % becomes %25. The API decodes exactly once, then validates the original 1–512 Unicode code point identifier and checks ownership. Plus is not a space escape. Invalid escapes, UTF-8, null characters or decoded identifiers return VALIDATION_ERROR. Maximum encoded length is 6,144 ASCII characters. JSON authorUserId fields and response IDs are not encoded.
    
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

**author_user_id:** `str` — UTF-8 percent-encoded author ID from your application. Encode once with JavaScript encodeURIComponent(id) or Python urllib.parse.quote(id, safe="") before supplying this HTTP header or SDK header argument. usr_42 stays usr_42; a literal % becomes %25. The API decodes exactly once, then validates the original 1–512 Unicode code point identifier and checks ownership. Plus is not a space escape. Invalid escapes, UTF-8, null characters or decoded identifiers return VALIDATION_ERROR. Maximum encoded length is 6,144 ASCII characters. JSON authorUserId fields and response IDs are not encoded.
    
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

<details><summary><code>client.project.comments.<a href="src/recoms/project/comments/client.py">list</a>(...) -> ProjectComments</code></summary>
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

client.project.comments.list(
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

**author_user_id:** `typing.Optional[str]` — Optional exact external author identity filter, for a user's comment history. Combines with status/search/sort and must remain unchanged when continuing a cursor.
    
</dd>
</dl>

<dl>
<dd>

**search:** `typing.Optional[str]` — Text search in comment content; at most 512 Unicode code points. Omit or use an empty string for no text filter.
    
</dd>
</dl>

<dl>
<dd>

**status:** `typing.Optional[ProjectCommentStatus]` — Filter by latest revision decision or deleted node. ALL and unpublished states require moderation access.
    
</dd>
</dl>

<dl>
<dd>

**sort:** `typing.Optional[ProjectCommentSort]` — Creation time descending or ascending, total reactions descending, or published direct reply count descending; identifier breaks ties.
    
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

Approve or reject the specified latest PENDING or FAILED revision. Requires comments:review. Approval publishes that revision; rejection retains an earlier approved version. A key decision has no verified moderator-account attribution. A stale or already decided revision returns REVISION_ALREADY_REVIEWED. After a conflict or uncertain network outcome, use listProjectComments with comments:read to inspect the comment's latest revision ID and decision before considering another action. comments:review does not imply comments:read; without the read grant, stop and report the unknown outcome for an appropriately scoped caller to verify. Do not submit a decision for a different revision automatically. This operation does not replay Idempotency-Key. Available while frozen.
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

Test draft rules against example text using the current provider. Requires provider:test. No policy or comment is saved; no submission quota is consumed. The provider attempt is recorded and the separate provider-test rate limit applies. ALLOWED and DISALLOWED are successful test outcomes. Provider absence or technical failure returns PROVIDER_UNAVAILABLE rather than a content rejection. RATE_LIMITED includes Retry-After. An uncertain outcome cannot be recovered through a saved test result: do not retry automatically, because another attempt consumes provider capacity. An optional getProjectContext read requires the separate project:read permission and cannot recover this draft's verdict. Available while frozen.
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

Test and save an OpenAI-compatible HTTPS provider. Requires provider:configure. Unsafe outbound destinations are blocked. Settings are saved only after a successful connection test; a failed candidate does not replace the current configuration. preserve retains an existing credential or accepts an initial one; replace requires credential; remove clears authentication. Credentials are encrypted and never returned. After PROVIDER_CONFIGURATION_CHANGED or an uncertain outcome, use getProjectContext with project:read to inspect non-secret provider state before considering another save. provider:configure does not imply project:read; without that read grant, stop and report the unknown outcome for an appropriately scoped caller to verify. Matching URL/model and hasCredential do not prove which secret was saved; do not automatically repeat a credential replacement or removal. A refresh is not a concurrency lock. Wait Retry-After on RATE_LIMITED. Each connection test may consume provider capacity, but not submission quota. Available while frozen.
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

<details><summary><code>client.project.provider.<a href="src/recoms/project/provider/client.py">test</a>() -> TestProviderResponse</code></summary>
<dl>
<dd>

#### 📝 Description

<dl>
<dd>

<dl>
<dd>

Retry the saved provider connection. Requires provider:test. Availability and last test outcome may change and the attempt is recorded, but settings are not replaced. Missing/unreadable credentials or an unusable provider return PROVIDER_UNAVAILABLE; concurrent changes return PROVIDER_CONFIGURATION_CHANGED. Respect Retry-After on RATE_LIMITED. After a conflict or uncertain outcome, getProjectContext can inspect current connection-health state with the separate project:read permission; provider:test does not imply this grant. Without it, report that verification is unavailable. Current health is not a replay of this attempt. Do not retry automatically after an uncertain outcome: another call consumes provider-test capacity and may incur provider charges. This diagnostic call changes test state but does not consume comment submission quota. Available while frozen.
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

**request_options:** `typing.Optional[RequestOptions]` — Request-specific configuration.
    
</dd>
</dl>
</dd>
</dl>


</dd>
</dl>
</details>

