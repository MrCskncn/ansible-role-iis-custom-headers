IIS Custom Headers
==================

Add or remove custom HTTP Response Headers to IIS web sites on Microsoft Windows Server. 

Role Variables
--------------

The `iis_custom_headers` default variable defines which key and value to set for custom HTTP headers. If you wish to override it, you'll need to re-define all the headers that should be `present` or `absent`. Ansible will not merge your overridden variable with the defaults. The `X-Frame-Options` header is defined by the IETF in [RFC7034](https://tools.ietf.org/html/rfc7034) to improve web application security.

By default, Ansible will restart IIS at the end of the tasks because it usually assumes the `win_shell` execution results in a change; toggle `iis_custom_headers_restart_iis` to prevent the IIS restart.

    iis_custom_headers:
      - { site: "Default Web Site", name: "X-Frame-Options", value: "SAMEORIGIN", state: "present" }
      - { site: "Default Web Site", name: "X-Powered-By", value: "ASP", state: "absent" }
      - { site: "Default Web Site", name: "X-Powered-By", value: "ASP.NET", state: "absent" }

    iis_custom_headers_restart_iis: True

Example Playbook
----------------

The tasks rely on using Powershell's web server (IIS) administration cmdlets [Add-WebConfigurationProperty](https://technet.microsoft.com/en-us/library/ee790572.aspx) and [Remove-WebConfigurationProperty](https://technet.microsoft.com/en-us/library/ee790570.aspx).

  - name: Modify HTTP response headers
    hosts: win
    roles:
      - name: iis-custom-headers
        iis_custom_headers:
          - { site: "Default Web Site", name: "X-Frame-Options", value: "SAMEORIGIN", state: "present" }
          - { site: "Default Web Site", name: "X-Powered-By", value: "ASP", state: "absent" }
          - { site: "Default Web Site", name: "X-Powered-By", value: "ASP.NET", state: "absent" }
          - { site: "MySite", name: "Strict-Transport-Security", value: "max-age=31536000", state: "present" }
          - { site: "MySite", name: "X-Frame-Options", value: "SAMEORIGIN", state: "present" }
          - { site: "MySite", name: "X-Powered-By", value: "ASP.NET", state: "present" }
        iis_custom_headers_restart_iis: True

The result will add [customHeaders](https://www.iis.net/configreference/system.webserver/httpprotocol/customheaders) elements to the global XML configuration:

    <configuration>
       <system.webServer>
          <httpProtocol>
             <customHeaders>
                <add name="Strict-Transport-Security" value="max-age=31536000" />
                <add name="X-Frame-Options" value="SAMEORIGIN" />
             </customHeaders>
          </httpProtocol>
       </system.webServer>
    </configuration>


Requirements
------------

* Windows Server 2012 or 2012 R2 or 2016
* IIS

Dependencies
------------

None.

License
-------

BSD
