the way case studies works is:
deskstructure has a list for Case Studies.
The list specifies a listitem for the landing page (of document type archivePage) with documentId caseStudiesPage
The list also has a listitem for the documentList of caseStudies
If you want the same structure for a new type of document called `solution` (instead of `page` or `caseStudy`) it'll look something like this:

```S.listItem()
    .title('Solutions')
    .icon(HiOutlineDocumentReport)
    .child(
      S.list()
        .title('Solutions')
        .items([
          S.listItem()
            .title('Landing page')
            .icon(HiOutlineDocumentText)
            .child(
              S.document()
                .schemaType('archivePage')
                .documentId('solutionsArchivePage')
            ),
          S.listItem()
            .title('Solutions')
            .icon(HiOutlineFolderOpen)
            .child(
              S.documentTypeList('solution')
                .title('Solutions')
            ),
        ])
    ),```

If i'm understanding correctly, you want the `solution` to basically use the same fields etc as `page` . If so, you'll need to copy the schema from `page` to a new document and rename, as you said.
To get it working on the frontend, you'll need to copy the structure in `pages/case-studies` (i.e. duplicate that entire folder, rename to `solutions`). The `index.js` file will show your landing page, make sure to update the slug. You can use an ordinary `page` in place of the `archivePage` if the structure is better for your needs, but don't forget to update in this file and also in the sanity deskStructure where it says `schemaType('archivePage')`. You might need a clever bit of logic in deskStructure to exclude this landing page from the ordinary `pages`, maybe use a [https://www.sanity.io/docs/create-a-link-to-a-single-edit-page-in-your-main-document-type-list#fa1e82fd32be](filter).
The `[slug].js` file will handle all the integrated.finance/solutions/* routes. Change any references from `case-studies`/`caseStudies` to `solutions`, and you'll also need to set up cases for solutions queries in the `getAllPageData` function in `lib/sanity.queries` - basically need an extra part like this:

```if (type === 'solutions') {
  pageData = await client.fetch(resourceQuery.solution(pageSlug, preview))
}```

and then also a new corresponding function export in `lib/sanity.queries.resources.js`

You'll also need to make sure to add a new `getAllSolutions()` function in `lib/sanity.querys` and use this in the `getStaticPaths` function inside `[slug].js` . This is necessary for build pregeneration, and also for the incremental static regeneration (which makes pages regenerate when something changes in sanity).

Finally, you'll need to update the webhook function in the sanity dashboard (not the editor studio - the dashboard where you set api keys etc).
