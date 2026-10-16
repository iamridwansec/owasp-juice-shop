# Multiple Likes: Like a Review at Least Three Times as the Same User

**Objective:** Like any product review at least three times as the same user.

## Background

Liking a review sends a request to `POST /rest/products/reviews` with a body identifying the review, e.g.:

```json
{"id": "ZQdzyRCbwQ4ys3PCG"}
```

This associates the logged-in user's email with the review and increments its like counter. The endpoint checks whether the current user has already liked the review and rejects repeat attempts:

```json
{"error": "Not allowed"}
```
(returned with HTTP `403 Forbidden`)

However, this check is vulnerable to a **race condition**: if multiple identical requests are sent at (almost) the same instant, the server may process them concurrently before the first one has finished recording the "already liked" state — allowing more than one to slip through and each increment the like counter.

## Steps Taken

### 1. Confirmed normal (single) like behavior

Liked a review once via the UI and confirmed a single `POST /rest/products/reviews` request was sent with the review's `id`.

### 2. Confirmed the replay protection

Attempted to send the same request again manually — received `403 Forbidden` with `{"error":"Not allowed"}`, confirming a normal repeat attempt is blocked.

### 3. Obtained a valid review ID and auth token

- Found a review's `id` via `GET /rest/products/<productId>/reviews`.
- Opened DevTools, liked a review once to capture a valid request, and copied the `Authorization: Bearer <token>` header value used.

### 4. Sent three simultaneous requests

Used a script to fire three `POST` requests to `/rest/products/reviews` with the same review `id`, at (essentially) the same time, so the server would process them concurrently within its narrow validation window:

```bash
TOKEN="<bearer token>"
REVIEW_ID="ZQdzyRCbwQ4ys3PCG"

for i in 1 2 3; do
  curl -s -X POST http://localhost:3000/rest/products/reviews \
    -H "Authorization: Bearer $TOKEN" \
    -H "Content-Type: application/json" \
    --data-raw "{\"id\":\"$REVIEW_ID\"}" &
done
wait
```

The `&` at the end of each `curl` command backgrounds it so all three fire essentially simultaneously, and `wait` pauses the script until all three have completed.

### 5. Verified the result

Checked the review's like count — it had increased by three instead of being capped at one, and the app showed the **Multiple Likes** challenge as solved.

## Result

Challenge solved — confirmed by the in-app success notification and the Score Board.

## Root Cause

The "already liked" check and the like-count increment are not handled atomically. When multiple requests for the same like arrive close enough together (within roughly a 150ms window), they can all pass the "not yet liked" check before any of them finishes recording that the like has occurred — a classic **Time-of-Check to Time-of-Use (TOCTOU)** race condition.

## Notes

The official solution also mentions [**RaceTheWeb**](https://github.com/aaronhnatiw/race-the-web), a dedicated race-condition testing tool, as an alternative way to fire the concurrent requests, using a `.toml` config specifying the request count, URL, body, and headers (including the bearer token).

## Status

Solved.
