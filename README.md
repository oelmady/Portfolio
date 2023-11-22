# Welcome
Hi and thank you for checking out my portfolio! I'm a MSc CS student at Northeastern University and graduate of Carnegie Mellon University. In between, I took CS courses at Tufts University. This portfolio reflects what I've learned across my academic career. I'm excited to add to it as I develop new skills.

## Projects
### Rideshare simulator: Object-Oriented Design (Fall 2023)
This project demonstrated the OOD principles we learned in Java. Here are some examples:

Functional programming (lambdas and streams)
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
Creational, structural, and/or behavioral design patterns
The only way to create Users was by creating Requests, and the only way to create Requests was by creating a RideRequestQueue. So this served to control how requests and user data was simulated. Likewise for the DriverDirectory and Driver data.
```java
    public RideRequestQueue(int numberOfRequests){
        super(numberOfRequests);
        IntStream.range(0, numberOfRequests).mapToObj(i -> new RideRequest()).forEach(this::add);
    }
```

MVC architecture
The User and Driver classes contain all the simulated data. The Sim Controller class is the only class that interacts with the data. From the user input, the only View comes from the SimController's output to the simulation.
```java
    System.out.println("Simulating 50 drivers and 50 riders:");
    SimController sim1 = new SimController(50, 50);
    System.out.println(sim1.printSimResults());
```

### Course

## Contact
Feel free to reach out:
oelmady@gmail.com
(203) 501-8244
linkedin.com/in/omar-elmady
