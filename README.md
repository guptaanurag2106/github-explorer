# GITHUB EXPLORER

This is a web application made with SVELTE and TAILWINDCSS which gives the top 'n' repositories of an organisation along with top 'm' committies of each of the repositories. top repositories are based on their fork count.
This is done by using the rest api v3 by github https://docs.github.com/en/rest.
Here we access the data using the fetch operation in javascript and then it sorts according to the fork count.

To run on local machine:

```bash
git clone  https://github.com/guptaanurag2106/github-explorer.git
cd github-explorer
npm install
```

To watch for changes and continually rebuild the package (this is useful if you're using [npm link](https://docs.npmjs.com/cli/link.html) to test out changes in a project locally):

```bash
npm run dev
```

To start the project

```bash
npm start
```
