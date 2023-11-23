# Welcome
Hi and thank you for checking out my portfolio! I'm a MSc CS student at Northeastern University and graduate of Carnegie Mellon University. In between, I took CS courses at Tufts University. This portfolio reflects what I've learned across my academic career. I'm excited to add to it as I develop new skills.

## Project Highlights
### Rideshare simulator: Object-Oriented Design (Fall 2023)
This project demonstrated the OOD principles we learned in Java by implementing a simulation for a rideshare backend. 

*Comparable and Comparator*

I used Comparable class because I was comparing a Java primitive (wait time). But if I was utilizing a more complicated comparison method, I would use a comparator with more functionality.
```java
public class Driver implements Comparable<Driver>

public class RideRequest implements Comparable<RideRequest> 
```

*Functional programming (lambdas and streams)*
```java
    Double avgWaitTime = this.completedRideList.stream()
            .mapToDouble(CompletedRide::getWaitTime)
            .average()
            .orElse(-1.0);
    Double avgRidesPerDriver = this.directory.stream()
            .mapToDouble(Driver::getRidesCompleted)
            .average()
            .orElse(-1.0);

```
*Creational, structural, and/or behavioral design patterns*

The only way to add new Users was by creating new Requests, which requires creating a RideRequestQueue. Likewise for the DriverDirectory and Driver data. This controls how new objects are created in the simulation. 
```java
    public RideRequestQueue(int numberOfRequests){
        super(numberOfRequests);
        IntStream.range(0, numberOfRequests).mapToObj(i -> new RideRequest()).forEach(this::add);
    }
```

*MVC architecture*

The User and Driver classes store simulated data (Model). The Sim *Controller* class alone controls the simulation. A user can *View* the SimController's output via print statements.
```java
    System.out.println("Simulating 50 drivers and 50 riders:");
    SimController sim1 = new SimController(50, 50);
    System.out.println(sim1.printSimResults());
```

### Unit testing framework: Software Engineering

```java
    /**
     * @param className of the {@link Class} to {@link Test} all methods with the
     *                  {@link Property} {@link java.lang.annotation.Annotation}
     * @return a {@link Map} of the name of the {@link Test} to the first array of
     *         {@link Objects} arguments to each test that returned false or raised
     *         an error. Does not store the resulting {@link Error}.
     */
    public static Map<String, Object[]> quickCheckClass(String className) {
        /*
         * This first step collects a Map < name of annotated method String, Method >
         * 
         */
        Object o = getClassInstance(className);
        Class<?> c = o.getClass();
        List<String> propertyMethodNames = new ArrayList<>();
        Map<String, Method> propertyMethods = new HashMap<>();
        for (Method m : c.getDeclaredMethods()) {
            if (m.isAnnotationPresent(Property.class)) {
                propertyMethodNames.add(m.getName());
                propertyMethods.put(m.getName(), m);
            }
        }
```

## Contact
Feel free to reach out:
oelmady@gmail.com
(203) 501-8244
linkedin.com/in/omar-elmady
