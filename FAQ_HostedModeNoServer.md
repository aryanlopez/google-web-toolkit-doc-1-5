# How do I use my own server in hosted mode instead of GWT's built-in Tomcat instance? #

If you do not need to use—or prefer not to use—the Tomcat instance embedded in GWT's hosted mode to serve up your Servlets for debugging, you can use the -noserver flag to prevent Tomcat from starting, while still taking advantage of hosted mode for debugging your GWT client code.

If you need the -noserver option, it is likely because your server-side code that handles your XMLHTTPRequest data requests requires something more, or just something different than Tomcat.  Here are some example cases where you might need to use -noserver:

  * You need an EJB container, which the embedded Tomcat server does not support.
  * You have an extensive Servlet configuration (with custom web.xml and possibly server.xml files) that is too inconvenient to use with the embedded Tomcat.
  * You are not using J2EE on the server at all (for example, you might be using JSON with Python).


When using the -noserver flag, your external server is used by the GWT Hosted Mode browser to serve up both your dynamic content, and all static content (such as the GWT application's host page, other HTML files, images, CSS, and so on.)  This allows you to structure your project files in whatever way is most convenient to your application and infrastructure.

Though your own external server handles all static content and dynamic resources, all browser application logic continues to be handled in Java, internal to hosted mode.  This means that you can continue to debug your client-side code in Java as usual, but all server-side requests will be served by your web or application server of choice. (If you are using an IDE such as Eclipse configured to integrate with GWT's hosted mode for debugging, then using -noserver will prevent you from automatically debugging your server code in the same debugger instance you use to debug hosted mode.  However, if the server software you use supports it, you can of course use an external debugging tools.)

Here is a step-by-step description of how to use -noserver:

  1. Configure your server however you need to; note the URL which contains the host page for your GWT application.
  1. Arrange all your static content files (such as the host HTML page, images, CSS, etc.) on the server however you like.
  1. Edit your hosted mode execution script (such as your Eclipse settings or the Project-shell script generated by the GWT applicationCreator) and add or update the following options:
    * Add the "-noserver" command line argument.
    * Change the URL at the end of the argument list to match the URL you recorded in step #1.
  1. Run the Project-compile script once, and copy the following files to your web server:
    * The "<Module Name>.nocache.js" file generated by the GWT compiler (_In GWT 1.3, copy the gwt.js file instead_).
    * The <Module Name>.html host HTML file
    * The hosted.html and history.html files output during compilation.
    * The `*.gwt.rpc` files generated by the GWT compiler (if your application uses GWT 1.4 RPC and the types serialized across the wire implement the `java.io.Serializable` marker interface).
    * The `clear.cache.gif` file (and all other generated image files) if you are using image bundles.

Be careful not to omit copying the files in Step #4:  This is an action you'll only have to take once, but it is a necessary step. However, one important point to note is that you may need to replace the .gwt.rpc file if your application uses GWT RPC and the types that your application serializes across the wire implement the java.io.Serializable interface. If these types are changed, or new serializable types are added to your RPC calls, the GWT compiler will generate a new .gwt.rpc file. You will need to replace the old file deployed on your web server with the newly generated file. Also, if the hosted.html and history.html files weren't output during compilation, you can copy the files from the gwt-user.jar file in the /com/google/gwt/core/public package.

To use -noserver mode productively, it will probably help you to understand [how to relocate cache/nocache files](FAQ_ChangeLocationGWTApplicationFiles.md).  Also, if the server you are using is running on a different machine than your local machine, you may also need to use the -whitelist option to instruct the hosted mode browser to allow requests to that machine.  (For security reasons, hosted mode by default only allows accesses to your local machine.)