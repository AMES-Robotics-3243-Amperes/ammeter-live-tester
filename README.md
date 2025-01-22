# Ammeter Live Tester
... is a program for running live integration and end to end tests on
FRC robots. To be used, it must be imported into the robot project, and
communicated with using the Ammeter Client. The ammeter client
can be accessed at https://github.com/AMES-Robotics-3243-Amperes/ammeter-client/

## What does it do?
Ammeter allows the user to create pieces of code (tests) that will be automatically run
when the robot is put into test mode. Each test will either succeed or fail, and the results
will be displayed to the user. Tests are grouped into test groups, often by subsystem.
Test can take many forms. They run within the context of the robot, and so can be affected by
each other or other robot code.

## How do I use it?
You can find a usage guide at https://docs.google.com/document/d/1pkQpg885VpZ4l_gevL5mJ7HXohLGMxDvgkKScSJyrS0/edit?usp=sharing
An installation guide follows as well.

## Feature List
* SubsystemBaseTestable class, which automatically queues itself and provides a simple API
  to run tests within a subsystem.
* Test group interface, allowing custom configurations for test groups
* Test interface, allowing custom test configurations
* Test utilities, which include:
  * Assertions, which will fail a test if two things are unequal
  * User question utilities, which send a binary question to the client, so that the user
    can provide information about the robot's state (useful for verifying encoders and motors)
  * Stock test classes, which act as formats tests can take, including:
    * Instant test (runs a piece of code exactly once)
	* One phase test (runs a piece of code until a condition is met)
	* Multiphase test (moves through sections of code as their conditions are met)
	* To be integrated - Timed test (moves through sections of code according to durations)
	* To be integrated - Combined test (Combines multiple tests into one, running them one by one)

## Installation
1. Download the files from this repo.
2. Place them into the PROJECT/src/main/java/frc/robot directory. This will replace the Robot.java file.
   *If you use a nonstandard Robot.java, merging will be necessary instead of replacement.*
3. Verify that the code compiles.
4. Acquire the Ammeter Client, or learn to use netcat to run the protocol manually.

## Protocol
The protocol used by Ammeter for TCP communication between the client and tester is fairly simple. In fact, 
because of its simplicity, and its use of solely UTF text in communication, a simple TCP connection program 
such as netcat can be used in place of either of end of the connection.

Communications are newline separated. Communication proceeds as follows:
| rio ALT | TCP Communication | Client |
|:--------|:-----------------:|-------:|
| Robot code loaded               |                                | Client started                        |
| Opens TCP listener on port 5809 |                                | Waits for user to command connection  |
|                                 | &#8592; Initial TCP connection | User commanded connection             |
| Holds connection                |                                |                                       |
| Tests start                     | "TestGroup1" &#8594;           |                                       |
|                                 | "TestGroup2" &#8594;           |                                       |
|                                 | "TestGroup3" &#8594;           |                                       |
|                                 | "END_SELECTION" &#8594;        |                                       |
|                                 |                                | Queries user for test group selection |
|                                 | &#8592; "TTF"                  | User gives selection                  |
|                                 | (In order, T for "run this group", F for "don't run this group") |     |
| Begins tests                    |                                |                                       |
| Test requests user input        | "BEGIN_QUESTION" &#8594;       |                                       |
|                                 | "Your question here?" &#8594;  |                                       |
|                                 | "True option" &#8594;          |                                       |
|                                 | "False option" &#8594;         |                                       |
|                                 |                                | Queries user with provided question   |
|                                 | &#8592; "T" or "F"             | User gives selection                  |
|                                 | (Only binary questions are allowed currently) |                        |
| Tests finish                    |                                |                                       |
| Results generated               | "G:TestGroup1" ("G:" followed by group name) &#8594; |                 |
|                                 | "S:SucceedingTest" &#8594;     |                                       |
|                                 | "That test's detail message, if present" &#8594; |                     |
|                                 | "F:FailingTest" &#8594;        |                                       |
|                                 | "" (No detail message) &#8594; |                                       |
|                                 | "G:TestGroup2" (Empty groups are allowed) &#8594; |                    |
|                                 | "G:TestGroup3" &#8594;         |                                       |
|                                 | "N:NotRunTest" &#8594;         |                                       |
|                                 | "Dependencies not correct" (Common detail message) &#8594; |           |
|                                 | "END_RESULTS" &#8594;          |                                       |
|                                 |                                | Displays results to user              |
|                                 |                                | Resets to beginning state             |
| Test mode disabled              |                                |                                       |
| Server shutdown and restarted   |                                |                                       |

## Contributing
Contributions are very welcome! You can contribute by...
* Solving issues or making improvements and submitting a pull request.
* Adding issues for problems you encounter or features you would like.

If you would like to know more about the project, or how you can contribute, contact
hydrogenhone+ammeter@gmail.com or
ames.amperes@gmail.com

Please share any improvements you make! Together we can build better tools for FIRST!
Note that the GNU GPLv3 license that this program is under prohibits the distribution of
closed source versions of the project.

## Planned features
* Include further test classes
* Improve quality of life
* Improve connection robustness and error handling

## Acknowledgements
### Creator
The Ammeter client and Ammeter Live Tester were created by Hale Barber, of team 3243, the AMES Amperes.
### Contributors
### Other Sources
* The Ammeter Live Tester is reliant on WPILib for much of the robot framework it interacts with. Thank you
  to the developers of WPILib!