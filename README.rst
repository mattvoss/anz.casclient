=====================
 anz.casclient README
=====================

:author:    jiangdongjin
:contact:   eastxing@gmail.com
:date:      2010/09/25
:abstract: This is a Zope PAS plugin that authenticates users against a
           CAS (Central Authentication Service) server.

.. contents::
.. sectnum::

Introduction
============
anz.casclient is a PAS plugin that authenticates users against a CAS
(Central Authentication Service) server.

Overview
========
anz.casclient implements a new PAS plugin 'Anz CAS Client'. It enables you
to integrate your Zope sites into your CAS SSO solutions.

Credits
========
Thanks to those guys who developed the following products, without your
works anz.casclient will never happen.

- CAS_
- `JA-SIG CAS Client for Java 3.1`_
- CAS4PAS_

.. _CAS: http://www.jasig.org/cas
.. _`JA-SIG CAS Client for Java 3.1`: https://wiki.jasig.org/display/CASC/CAS+Client+for+Java+3.1
.. _CAS4PAS: http://plone.org/products/cas4pas

Comparison with CAS4PAS
=======================
CAS4PAS was the first (if not the only) CAS client used in Zope world, but it
only partially implemented the CAS
`protocol <http://www.jasig.org/cas/protocol>`_, hence anz.casclient.

anz.casclient has some advantages:

- anz.casclient provides full CAS 1.0/2.0 protocol implementation.
- anz.casclient implements Single-Sign-Out.
- anz.casclient provides a framework similar to the official java 
  client implementation, which will make it easy to follow the evolution of
  CAS client.

Requirements
============
- Plone 3 or Plone 4
- ZODB3>=3.8.3
- zope.proxy>=3.4.1
- zope.bforest

Installation
============
To install anz.casclient into the global Python environment (or a
workingenv), using a traditional Zope 2 instance, you can do this:

* When you're reading this you have probably already run 
  ``easy_install anz.casclient``. Find out how to install setuptools
  (and EasyInstall) here:
  http://peak.telecommunity.com/DevCenter/EasyInstall

* Create a file called ``anz.casclient-configure.zcml`` in the
  ``/path/to/instance/etc/package-includes`` directory.  The file
  should only contain this::

    <include package="anz.casclient" />

Alternatively, if you are using zc.buildout and the
plone.recipe.zope2instance recipe to manage your project, you can do this:

* Add ``anz.casclient`` to the list of eggs to install, e.g.:

::

    [buildout]
    ...
    eggs =
        ...
        anz.casclient
       
* Re-run buildout, e.g. with:

::

    $ ./bin/buildout
        

How to use anz.casclient
========================

Create 'Anz CAS Client' plugin
------------------------------
Go into ZMI, {your plone site}/acl_users and add an 'Anz CAS Client' instance;
choose any Id you like, for example 'anz_casclient'.

Configure 'Anz CAS Client' plugin
---------------------------------
Go into {your plone site}/acl_users/anz_casclient; in the 'Activate' tab 
activate all four interfaces.

Click 'Authentication' to configure 'Authentication Plugins'; move
'anz_casclient' to the top.

Click 'Challenge' to configure 'Challenge Plugins'; move 'anz_casclient'
to the top.

Click 'Extraction' to configure 'Extraction Plugins'; move 'anz_casclient'
to the top.

Go into 'Properties' tab to configure CAS related properties. 

==============================  ===========  ==============================
Property                        Required     Note
serviceUrl                      False        An identify of the current service;
                                             CAS will redirect to here
                                             after login. Setting this explicitly
                                             rather than determining it automatically
                                             from the HTTP request gives us more
                                             security assurance -- see
                                             `here <https://wiki.jasig.org/display/CASC/CASFilter>`_.
casServerUrlPrefix              True         The start of the CAS server URL.
useSession                      False        Whether to store the Assertion
                                             in session or not. If sessions
                                             are not used, a proxy granting
                                             ticket will be required for
                                             each request. Defaults to True.
renew                           False        If set to True, CAS will ask the
                                             user for credentials again to
                                             authenticate, regardless of existing
                                             sessions.  This may be used
                                             for high-security applications.
                                             Defaults to False.
gateway                         False        If set to True, CAS will not
                                             ask the user for credentials.
                                             If the user has a pre-existing
                                             single sign-on session with CAS,
                                             or if a single sign-on session
                                             can be established through
                                             non-interactive means (i.e.
                                             trust authentication), CAS MAY
                                             redirect the client to the URL
                                             specified by the "service"
                                             parameter, appending a valid
                                             service ticket. (CAS also MAY
                                             interpose an advisory page
                                             informing the client that a CAS
                                             authentication has taken place.)
                                             If the client does not have a
                                             single sign-on session with CAS,
                                             and a non-interactive
                                             authentication cannot be
                                             established, CAS MUST redirect
                                             the client to the URL specified
                                             by the "service" parameter with
                                             no "ticket" parameter appended
                                             to the URL. If the "service"
                                             parameter is not specified and
                                             "gateway" is set, the behavior
                                             of CAS is undefined. It is
                                             RECOMMENDED that in this case,
                                             CAS request credentials as if
                                             neither parameter was specified.
                                             This parameter is not compatible
                                             with the "renew" parameter.
                                             Behavior is undefined if both
                                             are set to True. See details
                                             `here <https://www.apereo.org/cas/client-integration/gateway>`_.
                                             Defaults to False.
ticketValidationSpecification   True         Which CAS protocol to use for
                                             validating the ticket,
                                             one of ['CAS 1.0','CAS 2.0'].
proxyCallbackUrlPrefix          False        The start of the proxy callback
                                             url. You should point it to the
                                             current plugin with protocol
                                             'https'. The result url will be
                                             '{proxyCallbackUrlPrefix}/proxyCallback'.
                                             If set, it means this service
                                             will be used as a proxy to
                                             access a back-end service on
                                             behalf of a particular user.
acceptAnyProxy                  False        Whether any proxy is OK.
Allowed Proxy Chains            False        Allowed proxy chains. Each
                                             acceptable proxy chain should
                                             include a space-separated list
                                             of URLs. These URLs are
                                             the proxyCallback parameter passed
                                             to the proxy.
==============================  ===========  ==============================

Example configuration:

- Set 'serviceUrl' to 'http://{my plone site domain}:{port}/plone'
- Set 'casServerUrlPrefix' to 'https://{my cas server domain}:{port}/cas'
- Set 'useSession' to True
- Set 'renew' to False
- Set 'gateway' to False
- Set 'ticketValidationSpecification' to 'CAS 2.0'
- Set 'proxyCallbackUrlPrefix' to 'https://{my plone site domain}:{port}/plone/acl_users/anz_casclient'
- Set 'acceptAnyProxy' to False
- Set 'Allowed Proxy Chains' to None

Configure 'CAS login' entrance
------------------------------
If you use the 'Log in' link at the upper-right of the Plone page to login, you
should hide the stock Plone 'Log in' action first. Then add a new one named
'CAS log in' there, set URL(Expression) to
**'string:${globals_view/navigationRootUrl}/caslogin'**

Then add a Script(Python) named '**caslogin**' into 'portal_skins/custom',
with contents like:

::

 ## Script (Python) "caslogin"
 ##bind container=container
 ##bind context=context
 ##bind namespace=
 ##bind script=script
 ##bind subpath=traverse_subpath
 ##parameters=
 ##title=CAS Login
 ##
 request = container.REQUEST

 portal = context.portal_url.getPortalObject()
 plugin = portal.acl_users.anz_casclient

 if plugin.casServerUrlPrefix:
     url = plugin.getLoginURL() + '?service=' + plugin.getService()
     if plugin.renew:
         url += '&renew=true'
     if plugin.gateway:
         url += '&gateway=true'

     request.RESPONSE.redirect(  url, lock=1 )

If you use the 'login portlet' to login, you should remove the stock Plone
'login portlet' first so as not to confuse users. Then you should write a
new 'CAS login portlet' to authenticate users against CAS or customize
collective.castle_ to work with anz.casclient.

.. _collective.castle: http://plone.org/products/collective.castle/ 

Configure 'CAS logout' entrance
-------------------------------
If you use the 'Log out' link at the upper-right of the Plone page to logout,
you should hide the stock Plone 'Log out' action first. Then add a new one
named 'CAS log out' there, set URL(Expression) to
**'string:${globals_view/navigationRootUrl}/caslogout'**

Then add a Script(Python) named '**caslogout**' into 'portal_skins/custom',
with contents like:

::

 ## Script (Python) "caslogout"
 ##bind container=container
 ##bind context=context
 ##bind namespace=
 ##bind script=script
 ##bind subpath=traverse_subpath
 ##parameters=
 ##title=CAS Logout
 ##
 from Products.CMFCore.utils import getToolByName
 
 request = container.REQUEST
 portal = context.portal_url.getPortalObject()
 cas_client_plugin = portal.acl_users.anz_casclient

 mt = getToolByName( context, 'portal_membership' )
 mt.logoutUser( REQUEST=request )
 
 request.RESPONSE.redirect( cas_client_plugin.casServerUrlPrefix + '/logout' )

How to use proxy authentication
===============================
Proxy authentication is added by CAS 2.0; for its uses,
see the details `here. <https://www.apereo.org/cas/proxy-authentication>`_


1. Create two plone sites in one Zope instance, called **plone** and
   **backend**.
2. Create and configure an 'Anz CAS Client' plugin on each of them (make sure both sites
   can authenticate users against your CAS server).
3. anz.casclient includes a simple example to show how to use it, but it needs
   you to do a little customization. Open
   **anz.casclient/anz/casclient/proxyauthexample/view.py** with your
   favorite editor, find the **__init__** method and modify it to suit your
   situation:

::

 def __init__( self, context, request ):
     super(ProxyAuthExampleView, self).__init__( context, request )
        
     # eg. http://xx.xx.xx.xx:8080/backend
     self.BACK_END_SERVICE_URL = 'http://{domain of your zope instance}:{port}/backend'
        
     # eg. /plone/acl_users/anz_casclient
     self.PATH_TO_PROXIER_PLUGIN = '/plone/acl_users/anz_casclient'
        
     # eg. /backend/acl_users/anz_casclient
     self.PATH_TO_BACK_END_PLUGIN = '/backend/acl_users/anz_casclient'

4. After that restart your Zope, open a browser and login into site
   **plone** ( suppose user name is **tom** ).
5. Modify the location in your browser to
   **http://{domain of your zope instance}:{port}/plone/@@proxyAuthExample/getUserInfoFromTargetService**
   and click Enter, if all things goes well, you'll see:

::

 Hello, tom!

ToDo
====
* Add automation tests ( I really don't know how to automation test this
  kind of package :) )
