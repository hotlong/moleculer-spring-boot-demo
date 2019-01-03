# Moleculer Java demo project for Spring Framework

The project demonstrates the framework of a functioning Moleculer-based application. The application is launched and configured using the Spring Framework, the configuration files are located in the "cfg" directory.

The project can be imported into Eclipse IDE.

The project also includes a "buildInstaller" Gradle command to create a **Windows Installer** from the project, and it will install the finished application as a 64-bit **Windows Service**.

The Windows Service creates a Moleculer Node that can be connected to another **Java or NodeJS-based** Moleculer Node. The connection parameters can be specified in the "cfg/moleculer.config.xml" file and and in the files of the "cfg/transporter" directory.

**Build Windows Installer**

The project does not include any transporter libraries (JARs). If you want to use transporters (such as Redis or NATS) the transporter libraries should be listed in the project dependencies list (in the "build.gradle" file). For example, to use NATS, remove comment prefix from the line of the NATS dependency.

![image](docs/dependency.png)

In addition, you should specify in the "cfg/moleculer.config.xml" which transporter will be started. You can enable the telnet-based developer console in the same configuration file:

![image](docs/config.png)

To create the installer, type "gradlew buildInstaller" in the project directory:

![image](docs/gradlew.png)

The executable installer will be generated into the "installer/dist" directory, as "moleculer_setup_1.0.0.exe". When you run the installer, the executable Moleculer application is created in the proper directory structure.

![image](docs/installer1.png)

![image](docs/installer2.png)

![image](docs/installer3.png)

The Molecular service appears in the list of Windows Services:

![image](docs/service.png)

If you start a NodeJS-based Moleculer node, you can use the REPL console to test any Moleculer Action in Java node:

![image](docs/getobject.png)

The java-based Moleculer node's REPL console can be accessed via telnet. To do this, type "telnet localhost" command, then enter your user name and password (defaults are "admin" / "admin"):

![image](docs/info.png)

**Make your own service**

Copy the following code snippet to the "my.services" package:

```java
package my.services;

import org.springframework.stereotype.Controller;

import io.datatree.Tree;
import services.moleculer.eventbus.Listener;
import services.moleculer.eventbus.Subscribe;
import services.moleculer.service.Action;
import services.moleculer.service.Name;
import services.moleculer.service.Service;

@Name("myService")
@Controller
public class MyService extends Service {

	// --- CALLABLE ACTION ---

	@Name("myAction")
	public Action action = ctx -> {

		// Read request
		String var1 = ctx.params.get("var1", "defaultValue");
		long var2 = ctx.params.get("var2", 0L);

		// Create response
		Tree rsp = new Tree();
		rsp.put("key", "value");
		return rsp;
	};

	// --- EVENT LISTENER ---

	@Subscribe("myEvent")
	public Listener myEventListener = data -> {

		// Read data
		boolean var3 = data.get("key", false);
	};

}
```

The "ctx.params", "rsp" and "data" variables are hierarchical [Tree structures](https://berkesa.github.io/datatree/) (~= JSONs). At boot time the Spring Framework will automatically register this service as distributed Moleculer Service, which can be called by other (Java or Node.js) nodes.

# License
Moleculer-java is available under the [MIT license](https://tldrlegal.com/license/mit-license).