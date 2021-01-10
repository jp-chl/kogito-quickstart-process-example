# Kogito process example

> _Based on [https://quarkus.io/guides/kogito](https://quarkus.io/guides/kogito)_

There are some caveats that are not mentioned in official documentation. Here there are the steps to fulfill this example sucessfully.

---

## Setup

> _I use VSCode_.

I recommend to install the following add-ons for VSCode:

* Quarkus: [https://marketplace.visualstudio.com/items?itemName=redhat.vscode-quarkus](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-quarkus)

* Kogito Bundle: [https://marketplace.visualstudio.com/items?itemName=kie-group.vscode-extension-kogito-bundle](https://marketplace.visualstudio.com/items?itemName=kie-group.vscode-extension-kogito-bundle)

## Create maven project

```bash
mvn io.quarkus:quarkus-maven-plugin:1.10.5.Final:create -DprojectGroupId=org.acme -DprojectArtifactId=kogito-quickstart -Dextensions="kogito"

cd kogito-quickstart
```

## Writing the application

```bash
mkdir -p src/main/java/org/acme/kogito/model
touch src/main/java/org/acme/kogito/model/Person.java

mkdir -p src/main/resources/org/acme/kogito
touch src/main/resources/org/acme/kogito/person-rules.drl
touch src/main/resources/org/acme/kogito/person.bpmn2
```

Replace ```Person.java``` content with the following code:

```java
package org.acme.kogito.model;

public class Person {

	private String name;
	private int age;
	private boolean adult;

	public String getName() {
		return name;
	}

	public void setName(String name) {
		this.name = name;
	}

	public int getAge() {
		return age;
	}

	public void setAge(int age) {
		this.age = age;
	}

	public boolean isAdult() {
		return adult;
	}

	public void setAdult(boolean adult) {
		this.adult = adult;
	}

	@Override
	public String toString() {
		return "Person [name=" + name + ", age=" + age + ", adult=" + adult + "]";
	}
}
```

Replace ```person-rules.drl``` content with the following code:

```java
package org.acme.kogito

import org.acme.kogito.model.Person;


rule "Is adult" ruleflow-group "person"

when
    $person: Person(age > 18)
then
    modify($person) {
    	setAdult(true)
    };
end
```

## Desing the process

You should end with the following process:

![Person Process](readme-images/person-process.png)


Go to ```person.bpmn2``` file.
To model this process yourself, just follow these steps:

1. Click anywhere but an object (Whole Process properties).
    * Set process name as "```Person Process```"
    * ID: ```persons```
    * Package: ```org.acme.kogito```
    * ProcessType: ```public```
    * Version: ```1.0```
    * Imports: Set custom ```org.acme.kogito.model.Person```
    * Define a Process Data / Process Variable with name ```person``` of type ```org.acme.kogito.model.Person```
2. Drag a Start Event and set its name as ```StartProcess```, with no Implementation/Execution SLA Due Date (blank).
3. Drag a Task / Business Rule from the palette and drop it next to start event, link it with start event. Go to its properties:
    * In General properties, set its Name as ```Evaluate person```
        * You have to set an Implementation/Execution rule in order to avoid this kind of error: "_Rule task "Evaluate person" is invalid: you did not set a unit name, a rule flow group or a decision model"_.
    * In Implementation/Execution properties:
        * Rule Language: ```DRL```.
        * Rule Flow Group: Click on the combo box and select "```New```", then write down ```person```.
    * In Data Assignments:
        * Set data input and output (map person process variable to input data with name person and same for data output) by clicking on "```+Add```" button and choosing Data Type as Custom (```org.acme.kogito.model.Person```) as ```Name``` and ```Source``` as well.
4. Drag a Gateway/Exclusive (XOR) from the palette and drop it next to the business rule task, link it with rule task.
5. Drag a Tasks/User Task from the palette and drop it next to the gateway, link it with gateway.
    * In General properties, set its Name as ```Special Handling for children```.
    * In Implementation/Execution properties:
        * Set its task name as ```ChildrenHandling```.
        * In Assignments, set ```Data Input and Assignments``` (map person process variable to input data with name person) by clicking on "```+Add```" button, choosing Data Type as Custom (```org.acme.kogito.model.Person```) and set ```person``` as ```Name``` and ```Source``` as well; ```No Data Outputs and Assignments``` this time.
6. Drag the End Events → End from the palette and drop it next to the user task, link it with the user task, and set its name as "```End Event 1```".
7. Drag the End Events → End from the palette and drop it next to the gateway, link it with the user task, and set its name as "```End Event 2```".
8. On the line between XOR Gateway and "Special Handling for children" Task:
    * In Implementation/Execution properties, set the Expression as: ```return person.isAdult() == false;```; with language Java.
9. On the line between XOR Gateway and "End Event 2":
    * In Implementation/Execution properties, set the Expression as: ```return person.isAdult() == true;```; with language Java.
10. Save the file.
---

# Auto generated README

## kogito-quickstart project

This project uses Quarkus, the Supersonic Subatomic Java Framework.

If you want to learn more about Quarkus, please visit its website: https://quarkus.io/ .

## Running the application in dev mode

You can run your application in dev mode that enables live coding using:
```shell script
./mvnw compile quarkus:dev
```

## Packaging and running the application

The application can be packaged using:
```shell script
./mvnw package
```
It produces the `kogito-quickstart-1.0.0-SNAPSHOT-runner.jar` file in the `/target` directory.
Be aware that it’s not an _über-jar_ as the dependencies are copied into the `target/lib` directory.

If you want to build an _über-jar_, execute the following command:
```shell script
./mvnw package -Dquarkus.package.type=uber-jar
```

The application is now runnable using `java -jar target/kogito-quickstart-1.0.0-SNAPSHOT-runner.jar`.

## Creating a native executable

You can create a native executable using: 
```shell script
./mvnw package -Pnative
```

Or, if you don't have GraalVM installed, you can run the native executable build in a container using: 
```shell script
./mvnw package -Pnative -Dquarkus.native.container-build=true
```

You can then execute your native executable with: `./target/kogito-quickstart-1.0.0-SNAPSHOT-runner`

If you want to learn more about building native executables, please consult https://quarkus.io/guides/maven-tooling.html.

# RESTEasy JAX-RS

<p>A Hello World RESTEasy resource</p>

Guide: https://quarkus.io/guides/rest-json
