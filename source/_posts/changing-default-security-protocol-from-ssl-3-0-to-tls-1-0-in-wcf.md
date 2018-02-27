---
title: Changing Default Security Protocol In WCF
date: 2015-08-24 21:32:09
tags:
  - app-domain
  - poodle attack
  - seperate app-domain
  - ssl
  - tls
  - wcf
---

As you may know, an exploit was recently detected on SSL 3.0 version, named as Poodle Attack. If you would like to see the details, you can visit [here](https://www.openssl.org/~bodo/ssl-poodle.pdf). After the exploit is declared, many of the authorities disabled SSL 3.0 connections right away and some of our integrations failed to provide data. Like many enterprise level platforms, our platform also had some external dependencies and operations that required data from these external sources started throwing timeout exceptions. We needed a solution to disable SSL 3.0 connections immediately and change our main protocol to TLS 1.0. If you would like to learn what we needed to change please keep reading.

![SSL-TLS-Diagram](/images/posts/2015/SSL-TLS-Diagram.png)

After a little research you can easily find that in WCF, [ServicePointManager](https://msdn.microsoft.com/en-us/library/system.net.servicepointmanager.securityprotocol.aspx) determines which security protocol will be used in a client/server connection. But the trick is [ServicePointManager](https://msdn.microsoft.com/en-us/library/system.net.servicepointmanager.securityprotocol.aspx) can only be set per application domain. (Not per request) This property has been reported to Microsoft but as you see from [here](https://connect.microsoft.com/VisualStudio/feedback/details/605185/cant-set-the-security-protocol-per-servicepoint), it is closed as "Won't fix". If your client applications work in different app-domains than setting [ServicePointManager](https://msdn.microsoft.com/en-us/library/system.net.servicepointmanager.securityprotocol.aspx) to TLS 1.0 is straight forward. You can see the MSDN example from [here](http://msdn.microsoft.com/en-us/library/system.net.securityprotocoltype.aspx). But if you have different clients gathered-up in a single solution than the problem starts to arise if you would like to use SSL 3.0 for some of the connections but TLS 1.0 for the rest.

 In this scenario, the integration services tier consists of many WCF web services with a single setup, under a single web site on IIS. Which means, IIS will determine which of these web services will operate on the same app-domain according to the load on the services. Since you can't control the app-domain placement, you also can't control which of these services will use TLS 1.0 over SSL 3.0 and we need a solution for that.

[This blog post](http://www.superstarcoders.com/blogs/posts/executing-code-in-a-separate-application-domain-using-c-sharp.aspx) suggests a great solution to execute any code in a seperate app-domain. Solution can be easily applied to the integration services that will use TLS 1.0 protocol so that they will be executed in a seperate app-domain, independent from IIS judgement.

``` csharp
public sealed class Isolated<T> : IDisposable where T : MarshalByRefObject
{
    private AppDomain _domain;
    private T _value;

    public Isolated()
    {
      _domain = AppDomain.CreateDomain("Isolated:" + Guid.NewGuid(),
          null, AppDomain.CurrentDomain.SetupInformation);
      Type type = typeof(T);
      _value = (T)_domain.CreateInstanceAndUnwrap(type.Assembly.FullName, type.FullName);
    }

    public T Value
    {
      get { return _value; }
    }

    public void Dispose()
    {
      if (_domain != null)
      {
          AppDomain.Unload(_domain);
          _domain = null;
      }
    }
}
```

It is straight forward to use what is suggested, but we needed some customization on AppDomain creation. Our integration services keeps connection details securely encrypted in database. And many of these integrations are invoked 3-4 times per second which means each instance will need to access the database to get connection details. We must prevent that for performance issues. An easy solution is creating an singleton AppDomain. So the AppDomain will live as long as there are integration instances in it and each integration service will have its own AppDomain. (So that the [ServicePointManager](https://msdn.microsoft.com/en-us/library/system.net.servicepointmanager.securityprotocol.aspx) can be set for SSL or TLS independently for each integration.) At the end expected visualization of AppDomains will be like this:

![AppDomain](/images/posts/2015/AppDomain.png)

Customization code is as follows:

``` csharp
public sealed class CustomAppDomain
{
    private static volatile AppDomain instance;
    private static object syncRoot = new Object();

    private CustomAppDomain() { }

    public static AppDomain Instance
    {
        get
        {
            if (instance == null)
            {
                lock (syncRoot)
                {
                    if (instance == null)
                    {
                        instance = AppDomain.CreateDomain("IsolatedAppDomain" + Guid.NewGuid(), null, AppDomain.CurrentDomain.SetupInformation);
                    }
                }
            }
            return instance;
        }
    }
}
```

# References

* [Executing Code in a Separate Application Domain Using C#](http://www.superstarcoders.com/blogs/posts/executing-code-in-a-separate-application-domain-using-c-sharp.aspx)
* [This POODLE Bites: Exploiting The SSL 3.0 Fallback](https://www.openssl.org/~bodo/ssl-poodle.pdf)
* [MSDN, ServicePointManager.SecurityProtocol Property](https://msdn.microsoft.com/en-us/library/system.net.servicepointmanager.securityprotocol.aspx)
* [MSDN, SecurityProtocolType Enumeration](http://msdn.microsoft.com/en-us/library/system.net.securityprotocoltype.aspx)
