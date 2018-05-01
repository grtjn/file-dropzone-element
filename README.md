# file-dropzone-element
Native Web Component for a dropzone to support uploading of binary files

This is essentially a refactored version of https://github.com/ryanjdew/ml-uploader to eliminate AngularJS, and allow different kinds of backends. Credit to @ryanjdew for ml-uploader.

Disclaimer: Work in Progress, and not tested in production. Feedback welcome though.

Check https://caniuse.com/ to verify how well Web Components is covered by current browsers. This component uses Custom Elements v1, (optionally) HTML Imports, HTML Templates, Shadow DOM v1 (gracefull decay would be possible).

## Installation

### CDN and polyfill

Simplest way:

    <link rel="import" href="https://cdn.rawgit.com/grtjn/file-dropzone-element/master/file-dropzone.html">

Likely together with this polyfill:

    <script src="https://cdn.rawgit.com/webcomponents/webcomponentsjs/master/webcomponents-hi.js" type="text/javascript" defer></script>

### NPM and html-loader

This component is also published on npm:

    npm install --save file-dropzone-element

Potentially combined with something like:

    <%= require('html-loader!../node_modules/file-dropzone-element/file-dropzone.html') %>

### Bower, wiredep, and gulp

This component is also published on bower:

    bower install --save file-dropzone-element

You can combine that with gulp and wiredep with something like:

    .pipe(gulpWiredep({
      fileTypes: {
        html: {
          block: /(([ \t]*)<!--\s*bower:*(\S*)\s*-->)(\n|\r|.)*?(<!--\s*endbower\s*-->)/gi,
          detect: {
            js: /<script.*src=['"]([^'"]+)/gi,
            css: /<link.*href=['"]([^'"]+)/gi,
            html: /<link.*href=['"]([^'"]+)/gi
          },
          replace: {
            js: '<script src="{{filePath}}"></script>',
            css: '<link rel="stylesheet" href="{{filePath}}">',
            html: '<link rel="import" href="{{filePath}}">'
          }
        }
      }
    }))

Add this to your index.html:

    <!-- bower:html -->
    <link rel="import" href="/bower_components/file-dropzone-element/file-dropzone.html">
    <!-- endbower -->

    <!-- @exclude -->
    <script src="https://cdn.rawgit.com/webcomponents/webcomponentsjs/master/webcomponents-hi.js" type="text/javascript" defer></script>
    <!-- @endexclude -->

This relies on the html-import polyfill loaded from CDN. If you prefer not to rely on that, or if your tooling builds minified versions of everything, you can include the html similarly to html-loader mentioned above in NPM, and strip off the polyfill using gulp-preprocessor roughly like this:

    .pipe(gulpReplace(/<link rel="import" href="([^"]+)">/g, '<!-- @include ../$1 -->'))
    .pipe(gulpPreprocess())

## Usage

Example:

    <file-dropzone
        button="false"
        multiple="multiple">
      <div slot="intro">
        <p><strong>Drop files here or click to select files.</strong></p>
        <em>(Files will be uploaded automatically)</em>
      </div>
    </file-dropzone>

Emits events 'drop', 'drop-all', 'file-dblclick'.

### Plaing HTML and JavaScript

- In your html:

        <file-dropzone
            button="false"
            multiple="multiple">
          <div slot="intro">
            <p><strong>Drop files here or click to select files.</strong></p>
            <em>(Files will be uploaded automatically)</em>
          </div>
        </file-dropzone>

        <script>
          function sendFile(e) {
            var progress = e.detail;
            console.log(["sendFile", progress]);
            progress.update(0);
            setTimeout(function() {
              progress.update(Math.random() * 100);
              setTimeout(function() {
                if (Math.random() > 0.5) {
                  progress.update(100);
                } else {
                  progress.error("upload failed");
                }
              }, Math.random() * 5000);
            }, Math.random() * 5000);
          }
          document
          .getElementsByTagName("file-dropzone")[0]
          .addEventListener("drop", sendFile);
        </script>

### Vue.js

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

### AngularJS

- In your html template:

        <file-dropzone
            button="false"
            multiple="multiple">
          <div slot="intro">
            <p><strong>Drop files here or click to select files.</strong></p>
            <em>(Files will be uploaded automatically)</em>
          </div>
        </file-dropzone>

- In your controller something like:

        UploadCtrl.$inject = ['$scope', '$document', 'MLRest', 'ngToast', '$state'];

        function UploadCtrl($scope, $document, mlRest, toast, $state) {

          var ctrl = this;

          function uploadFile(e) {
            var progress = e.originalEvent.detail;
            progress.update(25);
            mlRest.updateDocument(progress.file, {
              uri: '/uploads/' + progress.file.name,
              collection: 'uploads'
            }).then(function(response) {
              progress.update(100);
            }, function(error) {
              progress.error(error);
            });
          }

          $document.on('drop', uploadFile);

          $scope.$on('$destroy', function() {
            $document.off('drop', uploadFile);
          });

        }


## Test locally

- Uncomment the local import line in docs/index.html
- Run something like `python -m SimpleHTTPServer 8888` from project root
- Open http://localhost:8888/docs/

