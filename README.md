# Welcome
Hi and thank you for checking out my portfolio! I'm a MSc CS student at Northeastern University and graduate of Carnegie Mellon University. In between, I took CS courses at Tufts University. This portfolio reflects what I've learned across my academic career. I'm excited to add to it as I develop new skills.

## Sample of Projects
I've sampled some class projects that I'm particularly proud of. (To avoid plagiarism, I cannot post project files online!)
Each sample project: class (semester) [size] has code snippets and comments that explain how I implemented the project spec. If you'd like to learn more, I'd be happy to walk through the full project; just reach out.

I hope you find this helpful in learning more about my coding skills!

### Rideshare simulator: Object-Oriented Design (Summer 2023) [11 .java classes]
This project's specification was to implement simulation for a rideshare backend in Java. There were several design choices for implementing the simulation, including how to represent the drivers and riders. The goal was to make it as realistic to something like Uber or Lyft as possible. Below are snippets of 4 of the OOD concepts we implemented (15 in total).

*Comparable and Comparator*

I used the Comparable class to sort Driver objects in a custom queue class to get the first available driver for the ride request. Similarly, eachh RideRequest was sorted by the time it was requested.
```java
public class Driver implements Comparable<Driver>

public class RideRequest implements Comparable<RideRequest> 
```

*Functional programming (lambdas and streams)*

I used the Stream library from Java 8 to perform transformations on list-like objects, such as getting averages. 
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

The RideRequestQueue would create several RideRequest objects, which wrapped the simulated Request data. Likewise for the DriverDirectory and Driver data. 
```java
    public RideRequestQueue(int numberOfRequests){
        super(numberOfRequests);
        IntStream.range(0, numberOfRequests).mapToObj(i -> new RideRequest()).forEach(this::add);
    }
```

*MVC architecture*

The User and Driver classes store simulated data (Model). The Sim *Controller* class is the only interface for a user to input on the simulation. A user can *View* the SimController's output via print statements.
```java
    System.out.println("Simulating 10 drivers and 50 riders:");
    SimController sim1 = new SimController(10, 50);
    System.out.println(sim1.printSimResults());
```

### Multi-threaded metro simulation: Software Engineering (Fall 2022) [19 .java classes]
This project simulated a train system using Java threads. What stood out to me was how the train system could produce different outputs based on the internal thread queueing pattern. To make this work concurrently, I took a high-level view of a real train system: a train arrives at a station then stops, then passengers can board if they need to get on the train or deboard if they need to get off at that station, then the train closes its doors, and finally departs the station, heading for the next station. Organizing locks in a natural way made debugging more logical, because threads can be tricky to debug. I found it fun to design the components in the simulation, and to follow through on the implementation.

Below is the code snippet for how a passenger thread would run through the simulation. I used clear variable names, so even without seeing the rest of the code, it is straightforward to read through it. For clarity, I added comments explaining the logic behind some fields.
```java
    public void run(){
        while (nextStop != null) {
            if (boardedTrain == null){
                // the boardedTrain field, if null, indicates the passenger is at a station
                location.stationLock.lock();
                while (location.dockedTrain == null ||
                        !mbta.isGoingToStation(location.dockedTrain, nextStop)) {
                    try {
                        location.arrivingTrain.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                boardedTrain = location.dockedTrain;
                log.passenger_boards(p, boardedTrain, location);
                location.stationLock.unlock();
            } else {
                nextStop.stationLock.lock();
                // the passenger rides the train until it arrives at the nextStop in their journey. 
                while (nextStop.dockedTrain != boardedTrain) {
                    try {
                        nextStop.arrivingTrain.await();
                    } catch (InterruptedException e) {
                        e.printStackTrace();
                    }
                }
                log.passenger_deboards(p, boardedTrain, nextStop);
                boardedTrain = null;
                location = nextStop;
                nextStop.stationLock.unlock();
                // having arrived at a station, the passenger checks for the nextStop of their journey.
                // if null, they will have arrived at their destination, and the run() function terminates.
                nextStop = journey.poll();
            }
        }
    }
```


### Unit testing framework: Software Engineering (Fall 2022) [17 .java classes]
The framework implements two methods: testClass and quickCheckClass. Both methods utilize Java reflection to automatically test all the annotated methods in a given class. This project challenged me to carefully observe multiple layers of reflection and think more deeply about automation.

Below, the quickCheckClass method generates paramters for each testable method in the class according to the annotations. First, we Map a method name String to its Method java object.
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
An intermediate step is generating arguments for each method, which could have arbitrary parameters. This requires reflection and error handling. The final step of invoking the method on a combination of arguments is shown below. Failing tests are added to the final output Map<method name String, failing argument(s) Object[]>.
```java
    for (int i = 0; i < combinations; i++) {
        Object[] args = argsList.get(i);
        /*
         * to test the method,
         * invoke with args (Object[]) matching the parameter types
         * cast the result to boolean
         */
        try {
            boolean b = (boolean) c.getDeclaredMethod(name, m.getParameterTypes()).invoke(o, args);
            if (!b) {
                throw new Exception();
            }
        } catch (Exception e) {
            failingArgs.put(name, args);
            break;
        }
    }
```

### Six degrees of separation interactive simulation: Data Structures and Algorithms (Summer 2022) [4 .cpp files]

This project tested our understanding of graphs and their implementation in C++, as well as I/O using the command line. Each artist was a vertex in a graph of their musical collaborations. A user could find the shortest path between two artists by finding a chain of collaborations. The most fun part of this project was testing a large, well-connected graph to find paths between very different artists.

```c++
#ifndef __SIXDEGREES__
#define __SIXDEGREES__
/**
Purpose: to traverse the CollabGraph and interface with terminal inputs and outputs, allowing a user to find paths between artists in the collab graph.
**/

#include "CollabGraph.h"
#include "Artist.h"

#include <iostream>
#include <fstream>
#include <string>
#include <vector>
#include <stack>

using namespace std;

class SixDegrees{

public:
    SixDegrees();
    SixDegrees(CollabGraph g);
    ~SixDegrees();
    SixDegrees(const SixDegrees& other);
    SixDegrees &operator=(const SixDegrees rhs);
    
    void populate_graph(std::ifstream &data);
    void print_graph(std::ostream &out);

    string run(string command, vector<string> names);

    string bfs(Artist &source, Artist & target);
    string dfs(Artist &source, Artist & target);
    string not_search(Artist & source, Artist & target, vector<string> names);
private:
    CollabGraph graph;

    string print_path(Artist &source, Artist &dest);
    bool dfs_search(Artist &source, Artist &target, Artist &current);
    Artist find_artist(string name);
};

#endif
```


## Contact
Thank you for taking a look at this portfolio. I truly appreciate your time and consideration, and I hope you found these examples to reflect your expectations. If you believe that I could contribute to your project or team, feel free and reach out to set up a meeting. If you'd like me to walk you through any of my work, I would also greatly appreciate any advice or comments to help me improve my skills.

oelmady@gmail.com

(203) 501-8244

linkedin.com/in/omar-elmady
