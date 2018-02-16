# Warden SendGrid Integration

![Warden](http://spetz.github.io/img/warden_logo.png)

**OPEN SOURCE & CROSS-PLATFORM TOOL FOR SIMPLIFIED MONITORING**

**[getwarden.net](http://getwarden.net)**

|Branch             |Build status                                                  
|-------------------|-----------------------------------------------------
|master             |[![master branch build status](https://api.travis-ci.org/warden-stack/Warden.Integrations.SendGrid.svg?branch=master)](https://travis-ci.org/warden-stack/Warden.Integrations.SendGrid)
|develop            |[![develop branch build status](https://api.travis-ci.org/warden-stack/Warden.Integrations.SendGrid.svg?branch=develop)](https://travis-ci.org/warden-stack/Warden.Integrations.SendGrid/branches)

**SendGridIntegration** can be used for sending the emails via **[SendGrid](https://sendgrid.com)**.
Both, standard email messages and transactional templates are supported. You may use either API key or credentials (username and password) to connect to your _SendGrid_ account.

### Installation:

Available as a **[NuGet package](https://www.nuget.org/packages/Warden.Integrations.SendGrid)**. 
```
dotnet add package Warden.Integrations.SendGrid
```

### Configuration:

 - **WithDefaultSubject()** - default subject of the email message.
 - **WithDefaultMessage()** - default body of the email message.
 - **WithDefaultReceivers()** - default receiver(s) email address(es).
 - **WithDefaultTemplateId()** - default template id of the transactional template.
 - **WithDefaultTemplateParameters()** - default parameters of the transactional template. 
 - **WithHtmlBody()** - flag determining whether the message body should be of HTML type.
 - **WithEmailSenderProvider()** - provide a custom IEmailSender which is responsible for the sending email messages.

**SendGridIntegration** can be configured by using the **SendGridIntegrationConfiguration** class or via the lambda expression passed to a specialized constructor. 

### Initialization:

In order to register and resolve **SendGridIntegration** make use of the available extension methods while configuring the **Warden**:

```csharp
var wardenConfiguration = WardenConfiguration
    .Create()
    .IntegrateWithSendGrid("api-key", "noreply@system.com", cfg =>
    {
        cfg.WithDefaultSubject("Monitoring status")
           .WithDefaultReceivers("admin@system.com");
    })
    .SetGlobalWatcherHooks((hooks, integrations) =>
    {
        hooks.OnStart(check => GlobalHookOnStart(check))
             .OnFailure(result => integrations.SendGrid().SendEmailAsync("Monitoring errors have occured."))
    })
    //Configure watchers, hooks etc..
```

You may either use the API key or credentials (username and password). The last required parameter is the default sender email address e.g. _noreply@system.com_.

### Custom interfaces:
```csharp
public interface IEmailSender
{
    Task SendMessageAsync(string username, string password, SendGridMessage message);
    Task SendMessageAsync(string apiKey, SendGridMessage message);
}
```

**IEmailSender** is responsible for sending the email messages. It can be configured via the *WithEmailSenderProvider()* method. By default, it is based on the SendGrid Web instance.