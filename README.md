# file-dropzone-element
Native Web Component for a dropzone to support uploading of binary files

This is essentially a refactored version of https://github.com/ryanjdew/ml-uploader to eliminate AngularJS, and allow different kinds of backends. Credit to @ryanjdew for ml-uploader.

Disclaimer: Work in Progress, and not tested in production. Feedback welcome though.

Check https://caniuse.com/ to verify how well Web Components is covered by current browsers. This component uses Custom Elements v1, (optionally) HTML Imports, HTML Templates, Shadow DOM v1 (gracefull decay would be possible).

## Installation

Simplest way:

    <link rel="import" href="https://cdn.rawgit.com/grtjn/file-dropzone-element/master/file-dropzone.html">

Likely together with this polyfill:

    <script src="https://cdn.rawgit.com/webcomponents/webcomponentsjs/master/webcomponents-hi.js" type="text/javascript" defer></script>

Alternatively, 'require'/include it with a tool like Webpack:

    <%= require('html-loader!https://cdn.rawgit.com/grtjn/file-dropzone-element/master/file-dropzone.html') %>

## Usage

Static example:

    <file-dropzone
        button="false"
        multiple="multiple">
      <div slot="intro">
        <p><strong>Drop files here or click to select files.</strong></p>
        <em>(Files will be uploaded automatically)</em>
      </div>
    </file-dropzone>

Emits events 'drop', 'drop-all', 'file-dblclick'.

Embedding in Vue.js:

- Import or include the web component in your index.html
- Add `file-dropzone` to ignoreElements to stop it from squeaking:

        Vue.config.ignoredElements = ["file-dropzone"];

- Use it in any component:

        <file-dropzone
            button="false"
            multiple="multiple"
            v-on:drop="sendFile"
            v-on:drop-all="sendAllFiles"
            v-on:file-dblclick="openFile">
          <div slot="intro">
            <p><strong>Drop files here or click to select files.</strong></p>
            <em>(Files will be uploaded automatically)</em>
          </div>
        </file-dropzone>

## Test locally

- Uncomment the local import line in docs/index.html
- Run something like `python -m SimpleHTTPServer 8888` from project root
- Open http://localhost:8888/docs/

