
# LINKS

- https://lwn.net/Articles/694291/

# PORTALS

The portal package itself is named [xdg-desktop-portal](http://github.com/flatpak/xdg-desktop-portal) and implements a D-Bus service running under the name **org.freedesktop.portal.Desktop**. That service listens for specific requests from applications. In this initial 0.1 release, there are ten portals defined:

- **org.freedesktop.portal.Request**:
  a generic interface that is used by the portal service to keep the current portal request alive until it has been completed
- **org.freedesktop.portal.FileChooser**:
  an interface applications can use to trigger a file-open dialog
- **org.freedesktop.portal.OpenURI**: 
  an interface applications can use to initiate opening a URI in a web browser
- **org.freedesktop.portal.Print**:
  an interface applications can use to trigger the system print dialog
- **org.freedesktop.portal.Screenshot**:
  an interface applications can use to request a screenshot
- **org.freedesktop.portal.Notification**:
  an interface applications can use to send a desktop notification to the system or to withdraw a notification
- **org.freedesktop.portal.Inhibit**:
  an interface applications can use to inhibit the current desktop session from ending, idling, or suspending (such as might be wanted when the user is running a presentation)
- **org.freedesktop.portal.NetworkMonitor**:
  an interface that provides network status information to applications
- **org.freedesktop.portal.ProxyResolver**:
  an interface that provides network proxy information to applications
- **org.freedesktop.portal.Documents**:
  an interface that provides mediated access to a filesystem to an application

From that list, the FileChooser, OpenURI, Print, Screenshot, and Inhibit portals follow the traditional guidelines used by Android Intents and originally described by Poettering. 
**User interaction is required before the sandboxed application can complete any of the desired actions.**