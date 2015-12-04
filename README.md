# Elmah.Log4Net
This appender will allow log4net to be configured to send log messages to ELMAH directly. This way ELMAH can be the log manager of record for sites while still allowing specific logging parameters to be controlled with log4net as usual.

### How to use this component?
- Run ElmahScript.sql in your sql server DB to create ELmah related tables and Stored Proc. (if you are planning to store errors and logs at db level, which I prefer.)
- Add Elmah.Log4Net.dll in your project reference
- Add Elmah in your project thru Nuget
- Add Log4net in your project thru Nuget
- Add following log4net configuration to web.config

        <configSections>
                <section name="log4net" type="log4net.Config.Log4NetConfigurationSectionHandler, log4net" />
            <configSections>
            <configuration>
                <log4net>
                    <appender name="elmahappender" type="Elmah.Log4Net.ElmahLog4NetAppender, Elmah.Log4Net">
                      <layout type="log4net.Layout.PatternLayout">
                        <conversionPattern value="%date [thread] %-5level %logger - %message%newline" />
                      </layout>
                      <UseNullContext>False</UseNullContext>
                    </appender>
                    <root>
                      <level value="ALL" />
                      <appender-ref ref="elmahappender" />
                    </root>
                  </log4net>
            </configuration>
    
- You will have Elmah Settings already in web.config (no need to add this if it's already there). Once you add nuget for elmah, it will do web.config setting automatically.
   
            <sectionGroup name="elmah">
              <section name="security" requirePermission="false" type="Elmah.SecuritySectionHandler, Elmah" />
              <section name="errorLog" requirePermission="false" type="Elmah.ErrorLogSectionHandler, Elmah" />
              <section name="errorMail" requirePermission="false" type="Elmah.ErrorMailSectionHandler, Elmah" />
              <section name="errorFilter" requirePermission="false" type="Elmah.ErrorFilterSectionHandler, Elmah" />
            </sectionGroup>
            <system.web>
                <httpModules>
                  <add name="ErrorLog" type="Elmah.ErrorLogModule, Elmah" />
                  <add name="ErrorMail" type="Elmah.ErrorMailModule, Elmah" />
                  <add name="ErrorFilter" type="Elmah.ErrorFilterModule, Elmah" />
                </httpModules>
            </system.web>
            <system.webServer>
                <validation validateIntegratedModeConfiguration="false" />
                <modules>
                  <add name="ErrorLog" type="Elmah.ErrorLogModule, Elmah" preCondition="managedHandler" />
                  <add name="ErrorMail" type="Elmah.ErrorMailModule, Elmah" preCondition="managedHandler" />
                  <add name="ErrorFilter" type="Elmah.ErrorFilterModule, Elmah" preCondition="managedHandler" />
                </modules>
              </system.webServer>
              <elmah>
                <security allowRemoteAccess="true" />
                <errorLog type="Elmah.SqlErrorLog, Elmah" connectionStringName="ELMAHCloudDB" applicationName="eCitePulldownWS" />
              </elmah>
              <location path="elmah.axd" inheritInChildApplications="false">
                <system.web>
                  <httpHandlers>
                    <add verb="POST,GET,HEAD" path="elmah.axd" type="Elmah.ErrorLogPageFactory, Elmah" />
                  </httpHandlers>
                </system.web>
                <system.webServer>
                  <handlers>
                    <add name="ELMAH" verb="POST,GET,HEAD" path="elmah.axd" type="Elmah.ErrorLogPageFactory, Elmah" preCondition="integratedMode" />
                  </handlers>
                </system.webServer>
              </location>

- Add log4net configuration line in your "AssemblyInfo.cs" file.
    
         [assembly: log4net.Config.XmlConfigurator(Watch = true)]

- Add Elmah Configuration for SQL Server

         <elmah>
            <errorLog type="Elmah.SqlErrorLog, Elmah" connectionStringName="connString" applicationName="TTDev" />
          </elmah>
        connString = your SQL SERVER connection string.
        TTDev = Your application name to log in Elmah DB.

That's it....    

- In your code - 
        
        public bool UserAuthorized()
        {
            int state, mode;
            try
            {
                log.Info("Request to authorize user : " + AuthHeader.Username);
                log.Debug("xyz...");
                ....
                ....
                ....
            }
            catch (Exception ex)
            {
                Elmah.ErrorSignal.FromCurrentContext().Raise(ex);
            }
            
        }


### TODO
- I will make Nuget Package for this so that it's easy to find it at once place.

##### Thank You 
Bhavin Patel
- www.itsmebhavin.wordpress.com
- https://plus.google.com/collection/wLUrb (Android Hybrid App)
- https://plus.google.com/collection/EWXpb (HTML, Angular, Node, React etc)
- https://plus.google.com/collection/Iuknb (.NET)





