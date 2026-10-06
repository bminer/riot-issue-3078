# riot-issue-3078

Minimal reproduction for [riot/riot#3078](https://github.com/riot/riot/issues/3078): hot
module reloading with Parcel leaves reloaded components disconnected from their parent,
and remounted components render the version from the initial page load.

Uses only published packages: `riot` 10.1.6, `@riotjs/hot-reload` 10.0.0,
`@riotjs/compiler` 10.0.4, `@riotjs/parcel-transformer-riot` 10.0.0 and `parcel` 2.16.

## Steps

```bash
npm install
npm start
```

Open the page Parcel prints (http://localhost:1234 by default), then:

1. Click **increment**. The child shows `v1 - count: 1`, so props flow as expected.
2. In `child.riot`, change `v1` to `v2` and save. The child shows `v2 - count: 1`, so the
   hot reload itself worked.
3. Click **increment** twice.
   - **Expected:** `v2 - count: 3`
   - **Actual:** `v2 - count: 1`. The reloaded child no longer receives props from its
     parent.
4. Click **toggle child** to hide it, change `v2` to `v3` in `child.riot` and save, then
   click **toggle child** again.
   - **Expected:** `v3 - count: 3`
   - **Actual:** `v1 - count: 3`. The remounted child is the version from the initial page
     load, ignoring both hot updates.

The `count: 3` in step 4 shows that the parent's state kept updating all along; the child
from step 3 just stopped rendering it. No errors are logged to the console at any point,
and the page is never reloaded: these are genuine hot updates.
