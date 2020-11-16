# Installation

[![CircleCI](https://circleci.com/gh/briebug/ngrx-auto-entity.svg?style=svg&circle-token=d1d500027a81dda34d4ad75ae5fee38dd8953487)](https://circleci.com/gh/briebug/ngrx-auto-entity)

To get started, install the package using npm or yarn:

```text
npm i @briebug/ngrx-auto-entity@~0.5.0
```

```text
yarn add @briebug/ngrx-auto-entity@~0.5.0
```

## Requirements

The following peer requirements must also be installed when using NgRx Auto Entity:

| **Module** | **Minimum Required Version** | Supported Versions |
| :--- | :--- | :--- |
| `@angular/common` | 8.0.0 | 8.x, 9.x, 10.x\* |
| `@angular/core` | 8.0.0 | 8.x, 9.x, 10.x\* |
| `@ngrx/effects` | 8.0.0 | 8.x, 9.x, 10.x |
| `@ngrx/store` | 8.0.0 | 8.x, 9.x, 10.x |
| `rxjs` | 6.0.0 | 6.x |

```text
npm i @angular/{common,core}@^8.0 @ngrx/{effects,store}@^8.0 rxjs@^6
```

```text
yarn add @angular/{common,core}@^8.0 @ngrx/{effects,store}@^8.0 rxjs@^6
```

### Angular 10 Support \*

A quick note about Angular 10. While NgRx Auto-Entity has been tested with Angular 10, not every use case exhibited flawless support. There have been use cases where some uses of Auto-Entity with Angular 10 have worked just fine, and others that seemed to have issues. So we tentatively support Angular 10, but official guaranteed support is still pending. As such, we do not recommend you use Auto-Entity with Angular 10 in production environments at this time.

Further testing of Auto-Entity with Angular 10 is ongoing, and outstanding issues will be resolved as they are discovered. One of our key goals with Auto-Entity is to remain backwards compatible with prior versions of Angular as best we can. Due to certain TypeScript requirements, our minimum supported versions are Angular 8 and NgRx 8. We hope this will expand Auto-Entity viability to the broadest range of enterprise customers with established Angular projects, as well as cutting edge projects on the bleeding edge. Our compatibility goals may require more complex build, packaging and deployment in the future, and there may come a time when our minimum versions must change to keep abreast of the Angular platform. 

For now, Angular 10 support is tentatively there. If you run into issues, let us know by opening a ticket in our GitHub repo:

[https://github.com/briebug/ngrx-auto-entity/issues](https://github.com/briebug/ngrx-auto-entity/issues?q=is%3Aissue+is%3Aopen+sort%3Aupdated-desc)

