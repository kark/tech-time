## Objectives

I reviewed a fantastic [@emmenko's PR](https://github.com/commercetools/merchant-center-application-kit/pull/2565) which was a source of inspiration 😅

In my private projects I prefer using named ES6 exports rather than default exports - [here's the rationale](https://basarat.gitbook.io/typescript/main-1/defaultisbad).

I wanted to create a CLI to replace default exports with named exports and get some understanding of AST and codemod transformations with `jscodeshift`.

Other ideas for codemods: imports sorting

## Outcomes

**Codemod**

```js
function defaultToNamedExports(fileInfo, api) {
  const j = api.jscodeshift;
  const root = j(fileInfo.source);

  const defaultExportArr = root.find(j.ExportDefaultDeclaration);

  if (defaultExportArr.length > 0) {
    const defaultExportName = defaultExportArr.get(0).node.declaration.name;
    // delete the default export statement
    defaultExportArr.remove();

    // wrap variable declaration with named export statement
    root
      .find(j.VariableDeclaration, {
        declarations: [
          {
            id: {
              type: "Identifier",
              name: defaultExportName,
            },
          },
        ],
      })
      .replaceWith((p) => j.exportDeclaration(false, p.node));
  }

  return root.toSource();
}
```

**Helpful tools and links**

- [AST explorer](https://astexplorer.net/)
- [AST types](https://github.com/benjamn/ast-types)
- [Next.js codemods](https://nextjs.org/docs/advanced-features/codemods)

Libraries/gists:

- [`jscodeshift` docs](https://github.com/facebook/jscodeshift)
- [react codemods](https://github.com/reactjs/react-codemod)
- [js codemod](https://github.com/cpojer/js-codemod/)
- [js transforms](https://github.com/jhgg/js-transforms)
- [absolute-to-relative](https://gist.github.com/tibdex/f491a2d264ba14af5643de300957b4f9)

Articles:

- [Toptal article](https://www.toptal.com/javascript/write-code-to-rewrite-your-code)
- [Codemods for React TS](https://www.carlrippon.com/codemods-for-react-typescript/)

Other PRs:

- [remove-deprecated-modal-level-props](https://github.com/commercetools/merchant-center-application-kit/pull/2597)
