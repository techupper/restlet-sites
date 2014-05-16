Examples
========

Introduction
============

An example project download is provided that works both under Eclipse
and via ant build.  This sets up the basic framework for GWT compilation
and debugging in Hosted Mode, provides a basic Restlet-powered server,
and demonstrates how the compiled GWT application can be bundled into an
executable server JAR.

Download [Restlet GWT -- Simple
Example](/learn/guide/2.1#/298-restlet/version/default/part/AttachmentData/data "testGwtRestlet-2.0")
(application/force-download, 3.0 MB)

This is a simple example demonstrating some basic patterns for using
Restlet and GWT.  It produces an executable JAR file which depends only
on core Restlet libraries (included in "lib") to start a small Java Web
server on port 8888, which you can visit to access a  compiled GWT
application that, in turn, talks to the Web server.

You can also run the application in GWT Hosted Mode under Eclipse by
using the included SimpleExample.launch configuration; right click this
and choose Run As ... SimpleExample.

It is structured as an Eclipse project; you should be able to import it
into your Eclipse 3.3 or better workspace.  You can also run the ant
build script directly to produce the executable.

You must supply your own GWT binaries (it should work for 1.7 and upper
releases of GWT) ; update the Eclipse build path and/or the "gwt.sdk"
property in the ant build script to point to the GWT binaries for your
platform.

Zip content
-----------

Name

Description

src

Source files of both:

-   client page developped with GWT and Restlet, in package
    "org.restlet.example.gwt.client".
-   Web server application developped with Restlet that serves the
    compiled web page and hosts a resource requested from the Web page.

war

Directory where are located:

-   the web.xml configuration file (in "WEB-INF" subdirectory)
-   the client page and javascript files generated by the GWT
    compilation phase
-   the librairies the server side of this project depends on (in
    "WEB-INF/lib" subdirectory) - they are taken from the "gae" edition,
    release 2.0rc3

lib

Directory that contains the libraries of the client side of the project
- they are taken from the "gwt" edition, release 2.0rc3

Description
===========

GWT page
--------

You can find the source code of this page in directory
"src/org/restlet/example/gwt/client".

Once the server is running, this page can be accessed at the following
URL: "http://localhost:8080/TestGwtRestlet\_2\_0.html" .

This page is in charge to display several sample item such image,
button, etc organized in panels. All of these objects are instances of
GWT classes:

    // Define an image
    Image img = new Image("http://code.google.com/webtoolkit/logo-185x175.png");
    // Define a button
    final Button button = new Button("Click me");
    [...]
    // Define a panel
    VerticalPanel vPanel = new VerticalPanel();
    // We can add style names.
    vPanel.addStyleName("widePanel");
    vPanel.setHorizontalAlignment(VerticalPanel.ALIGN_CENTER);
    // Add image, button, tree
    vPanel.add(img);
    vPanel.add(button);

These class illustrates also how to add an asynchronous call with AJAX
inside the final Web page. It is as simple as to use a simple Restlet
client in order to request the "ping" resource located at URL
'"http://localhost:8080/ping" :

        // Add an AJAX call to the server
        ClientResource r = new ClientResource("/ping");

        // Set the callback object invoked when the response is received.
        r.setOnResponse(new Uniform() {
            public void handle(Request request, Response response) {
                try {
                    button.setText(response.getEntity().getText());
                } catch (IOException e) {
                     e.printStackTrace();
                }
            }
        });
        r.get();

Server side
-----------

Basically, the server s responsible to serve the generated page, and to
respond to asynchronous call described just above.

The generated page is served by a simple Directory Restlet from the
"bin" directory when the server is run under Eclipse.

The asynchronous call is delegated to the PingResource class which
inherits from the ServerRestlet Resource class. It simply answers to
requests with a line of text that contains the HTTP method of the
request and, if available, its challengeScheme if the user has provided
credentials.

    public class PingResource extends ServerResource {

        @Get("txt")
        public String toText() {
            StringBuilder sb = new StringBuilder("Restlet server alive. Method: ");
            sb.append(getRequest().getMethod());

            ChallengeResponse challengeResponse = getRequest()
                    .getChallengeResponse();
            if (challengeResponse != null) {
                sb.append("/ Auth. scheme: ");
                sb.append(challengeResponse.getScheme());
            }

            return sb.toString();
        }
    }
