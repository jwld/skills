# Your pending review

How a skill reads your pending review on a PR. Pointed at, never copied.

A pending review is visible only to its author, so its comments are points the
user has drafted but not yet sent. GitHub allows one per person per PR. The
query returns the PR's node id, and the pending review's id and comments when
there is one:

```bash
gh api graphql -f query='
  query($owner: String!, $repo: String!, $pr: Int!) {
    repository(owner: $owner, name: $repo) {
      pullRequest(number: $pr) {
        id
        reviews(first: 1, states: PENDING) {
          nodes {
            id
            comments(first: 100) {
              nodes { path line body }
            }
          }
        }
      }
    }
  }' \
  -f owner=OWNER \
  -f repo=REPO \
  -F pr=NUMBER
```
