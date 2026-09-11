# Lesson 13: Subsystems, Commands, and Your First Robot Behavior

Goal: Explain command-based structure and design a simple behavior (example: run a shooter, or drive) using a subsystem + command.

Time: About 45–60 minutes

You will learn:

- Subsystem = hardware + related sensors (the “thing”)
- Command = an action that *uses* subsystems (the “do this”)
- The scheduler runs commands every robot cycle
- addRequirements so two commands do not fight over the same motor
Before this lesson: Lesson 12 (WPILib project tour, TimedRobot, packages).

### Why this matters

Button mashers in teleopPeriodic do not scale. Command-based code lets you say: “While the operator holds A, run ShootCommand.” The scheduler handles starting, repeating, and stopping.

### Subsystem vs command

A subsystem has motors. A command tells those motors what to do for a while.

### The scheduler (simple picture)

```java
Every robotPeriodic (~20 ms):
    CommandScheduler.run()
```

```java
if isFinished(), call end() and stop that command
```



You saw CommandScheduler.getInstance().run() inside Robot.robotPeriodic().

### Anatomy of a command (WPILib `Command` )

This is inheritance: public class ShootCommand extends Command.

Example *idea* (not a full copy of team code):

```java
public class SpinShooterCommand extends Command {
    private final Shooter shooter;
```

```java
    public SpinShooterCommand(Shooter shooter) {
        this.shooter = shooter;
        addRequirements(shooter);
    }
```

```java
    @Override
    public void initialize() {
        shooter.run();
    }
```

```java
    @Override
    public void end(boolean interrupted) {
        shooter.stop();
    }
```

```java
    @Override
    public boolean isFinished() {
        return false; // run until the button is released / cancelled
    }
}
```



addRequirements(shooter) means: if another command also needs shooter, the scheduler will interrupt one of them. That prevents two commands from setting the same motor to different speeds.

Binding to a joystick (concept)

On this team, DriverJoystick / OperatorJoystick wire buttons to commands (for example “while held, run this command”).

You do not need every binding API today. Remember:

Button  Command uses Subsystem(s).

### Designing your first behavior

Work with a mentor. Pick something small:

Example A — Intake in

• Subsystem: Acquisition

• Command: while held, run intake rollers; on end, stop

Example B — Spin up shooter

• Subsystem: Shooter

• Command: start motors in initialize, stop in end

Example C — Drive slowly

• Subsystem: DriveTrain

• Command: set a small speed in execute, stop in end

Write on paper first:

1. Which subsystem(s)?

2. What happens in initialize?

3. What happens every execute?

4. When is it isFinished? (or does it run until cancelled?)

5. What must end do so the robot is safe (usually stop motors)?

Then implement with mentor review before deploying.

### Safety

• Always **stop** motors in end

• Test in **simulation** or on blocks / with bumpers before full-field driving

• Never deploy unreviewed motor code to a robot with people in the path

### Common mistakes

1. Forgetting addRequirements —— two commands fight over one motor

2. Forgetting to stop in end — motor keeps running after the button

3. Putting all logic in Robot.teleopPeriodic instead of a command

4. Command does not extend Command (or wrong import)

5. Huge command that talks to every subsystem split behaviors

## Try it yourself

Use the team robot project. Written design is required; coding Challenge 4 needs a mentor.

### Challenge 1 â€” Spot the pieces

Name one subsystem and one command from the team repo. What hardware vs what action?

### Challenge 2 â€” Read a command

Read a shoot/intake style command. When does `initialize` run vs `end`? What does `addRequirements` list?

### Challenge 3 â€” Design on paper

Design a `StopAllCommand` or `SlowDriveCommand` with `initialize` / `execute` / `end` / `isFinished`.

### Challenge 4 â€” With mentor

Implement a tiny command, bind it to a test button, run sim or a safe robot test.

### Check your understanding

1. What is the difference between a subsystem and a command?

2. Who calls execute() over and over?

3. Why call addRequirements?

4. Why stop motors in end?

Answers

1. Subsystem = robot part; command = action that uses part(s).

2. The command scheduler (from robotPeriodic).

3. So the scheduler knows which hardware this command owns and can cancel conflicts.

4. So the robot does not keep moving/spinning after the command stops.

### You made it

You started with println and finished at command-based robot structure. Keep practicing Java in small programs, and read team code in small chunks (one subsystem at a time).

Lesson complete. Next step is mentoring on the real robot: simulation, then deploy, then drive.
