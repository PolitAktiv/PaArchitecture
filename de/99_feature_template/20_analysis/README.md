# How URLs are calculated in Liferay

## Analysis Scope
This analysis describes, the concept, how (Friendly-)URLs are computed in Liferay

## Absolute URL
### From Request
 
* In Velocity: $portalUtil.getCurrentCompleteURL($request)
* In Java: String url = PortalUtil.getCurrentCompleteURL(request);
* In JSP: --
* In Javascript: --

### From Theme Display

* In Velocity: ---
  * directly: $theme_display.getURLPortal()$theme_display.getURLCurrent()
  * as variable: #set($url = $theme_display.getURLPortal() + $theme_display.getURLCurrent())
* In Java: String url = PortalUtil.getLayoutFriendlyURL(themeDisplay.getLayout(), themeDisplay);
* In JSP: --
* In Javascript:

## Relative URL (= Site URL)
### From Request 
* In Velocity:
* In Java:
* In JSP:
* In Javascript:

### From Theme Display
* In Velocity: $theme_display.getURLCurrent()
* In Java: String url = themeDisplay.getURLCurrent();
* In JSP:
* In Javascript:

### From Layout

(Note: Layout accessible from Theme Display)
* In Velocity:layout.getGroup().getFriendlyURL()
* In Java: String url = layout.getGroup().getFriendly();
* In JSP:
* In Javascript:
