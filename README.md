browser-node-loader
====================

A node.js style script loader for browser environments. Similar to browserify,
but without the bundling or any configuration files.

The command

    loader.js <output_file> <entry_point_1> [entry_point_2..]

generates one javascript file that includes the needed dependencies using DOM manipulation. 

`entry_point_1:`

    <html>
      <head>
        <script src="stacktrace.js" />
        <script src="output_file.js" />
      </head>
    <body>
      <script>
        var Backbone = require('backbone');
        var my_template = template('page.tpl.html');
        console.log(Backbone.version);
      </script>
    </body>
    </html>

### How it works

To include a file use require('foo.js') or template('bar.tpl.html'), this scripts wraps 
each include without modifying any code, such that they (hopefully) think they are being 
loaded inside node.js. We can't prohibit them from finding the global window object but it 
is usually not a problem.

What these scripts export through setting 'module.export' is available with require('dropbox') 
just as in node.js. 

Any keys set on the global object is deleted, although if there is no module.export then the 
first key is used as such.

Requires no configuration files. Path resolution is as follows:

1. Use, if any, the bower module of such name, determined by `bower list --paths` then
  * if bower module path is a directory try *directory_name/index.js*, *directory_name/directory_name.js*, *directory_name/lib/directory_name.js*
2. else files are relative to the file required from, and absolute paths are relative from the directory where 
   the build script is executed.
3. If not present '.js' is appended to the path of any require() call.

In order to behave correctly at runtime the script needs to know from where require/template/module.export is called 
to do so stacktrace.js is used to throw an exception, catch the trace and extract the filename.