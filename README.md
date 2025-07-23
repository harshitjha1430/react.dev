# react.dev – Blog Navigation Fix Fork

This is a fork of the [official react.dev repository](https://github.com/reactjs/react.dev) used to contribute a fix to the blog pagination logic.

## 🛠️ Purpose of This Fork

The goal of this fork is to resolve an issue with the **blog post navigation buttons**, where:
- The **"Next"** button incorrectly navigated to **older** posts.
- The **"Previous"** button incorrectly navigated to **newer** posts.

## ✅ Fix Summary

The direction logic was reversed. I fixed this by updating the `Pagination.tsx` logic to match the correct UX expectation:
- **Next → newer** post (forward in timeline)
- **Previous → older** post (backward in timeline)

### Example of Fixed Logic:


// Performs a depth-first search to find the current route and its previous/next route
function buildRouteMeta(
  searchPath: string,
  currentRoute: RouteItem,
  ctx: TraversalContext
) {
  if (currentRoute.path === searchPath) {
    ctx.route = currentRoute;
    ctx.order = ctx.currentIndex;
    ctx._foundRoute = true;
    return;
  }

  if (!ctx._foundRoute && currentRoute.path) {
    ctx.nextRoute = currentRoute; // newer post
  } else if (ctx._foundRoute && currentRoute.path && !ctx.prevRoute) {
    ctx.prevRoute = currentRoute; // older post
    return; // Stop once prevRoute is found
  }

  if (!currentRoute.routes) return;

  for (const route of currentRoute.routes) {
    buildRouteMeta(searchPath, route, ctx);
  }

  if (!ctx._foundRoute && currentRoute.path) {
    ctx.currentIndex++;
  }
}

View my Pull Request to the official React repo here:
reactjs/react.dev#7893
