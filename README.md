---
リリース日: 2025/11/25
備考: v3.5.1
バージョン: v3.6
URL: ''
---
# TEST9

**Staticmsから書き込みを行いました。**

ブランチの管理も加えて、誤爆クリアを阻止します。

### テーブル

| テーブル | 列 | 列2 |
| -- | -- | -- |
| 内容 | 行 | 行2 |
| 内容2 | 行 | 行2 |
| 内容3 | 行 | 行2 |
| 内容4 | 行 | 行2 |

### コード

```typescript
router.get("/api/pr-status", async (ctx) => {
  try {
    const prUrl = ctx.request.url.searchParams.get("prUrl");
    if (!prUrl) {
      ctx.throw(400, "Missing prUrl query parameter");
      return;
    }

    // Extract owner, repo, number from prUrl
    // Format: https://github.com/owner/repo/pull/number
    const regex = /github\.com\/([^/]+)\/([^/]+)\/pull\/(\d+)/;
    const match = prUrl.match(regex);

    if (!match) {
      ctx.throw(400, "Invalid PR URL format");
      return;
    }

    const [_, owner, repo, number] = match;
    const apiUrl =
      `https://api.github.com/repos/${owner}/${repo}/pulls/${number}`;
    const data = await githubRequest(apiUrl);

    ctx.response.body = {
      state: data.state, // "open" or "closed"
      merged: data.merged, // boolean
    };
  } catch (e) {
    console.error(e);
    ctx.response.status = 500;
    ctx.response.body = { error: (e as Error).message };
  }
});
```
