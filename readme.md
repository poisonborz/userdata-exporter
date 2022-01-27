
# poisonborz/user-data-exporter

A framework with the aim of easy creation of tools for personal data extraction off of web platforms - producing output directly from the web, or through a multiplatform command line utility. It is intended for offshoot projects that only need to additionally supply minimal platform-specific code, assuming changes here are downstreamed.
At the moment supports purely textual (json) data, possibility for including binary files are planned.

Note: this codebase needs additional extension files to successfully build/function.
[See my repositories](https://github.com/poisonborz?tab=repositories&q=data-exporter) for live examples, or read below to build your own.

## Extending for an export target

Currently you need to supply these 5 files:

- icon.ico - an icon/logo of the platform
- build.json - meta information, string properties to be embedded within the executables. Available properties: `name` (will also be executable file name) `, description, company, version, copyright`
- index.html - the website itself. Best review [existing project files]() first. Note how you should include some scripts, and the specific GUI elements of your platform. You can supply a `window.ownInputs` Preact component, with inputs toggling options properties, that are then passed to your `retrieval` script
- retrieval.js - the exporter script, providing the two methods below:
  - `getExport(utils, options)` method - It should be an async method returning a result that is considered the export data, and should be valid json. Called with 2 arguments:
    - `utils` - contains two helper methods: `utils.handleError(message)` for any exception (handled on command line and web gui) and `updateStatus(text, percent, success)` where text is a status message, percent an optional progress (0-1), and success is a boolean signaling operation result.
    - `options` - is an object containing arbitrary parameters needed for your exporter script (username, api key, etc.) that you can request via either command line or the web component above.
  - `getFileName(result)` method - should return string that will be the filename of all export files, called with `getExport` result for context.
- template.js - Should export a [DoT template](https://olado.github.io/doT/index.html) string, to be rendered with the result output.

Once these files are set:

- `npm start run` - run the export, providing options in `parameter=value` format. Extensions can require arbitrary options. The built-in ones are:
  - **mode** Optional.
    - _html_ - Generate a human readable `hn_export_USERNAME_DATE.html` file. As of now contains only story submissions and comments.
    - _json_ - Generate a machine readable `hn_export_USERNAME_DATE.json` file with the structure below.
    - _both_ - Default. Generate both files above.
  - **outputPath** - Optional. File path for the output files. Outputs to the root of the executable location when not specified.
- `npm run build` to build an executable for Windows, Linux and macOS in `/dist`
- host the /src folder for the web version (possibly GitHub Pages)
