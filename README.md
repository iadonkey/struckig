<div align="center">
  <h1 align="center">(ST)Ruckig</h1>
  <h3 align="center">
    Instantaneous Motion Generation for Robots and Machines.<br>
    Port of ruckig to Structured Text, TwinCAT 3.
  </h3>
</div>

<p align="center">
  <a href="https://github.com/stefanbesler/struckig/actions">
    <img src="https://github.com/stefanbesler/struckig/actions/workflows/build.yml/badge.svg" alt="Build/Test">
  </a>
  <a href="https://stefanbesler.github.io/struckig/Struckig/Constants.html">
    <img src="https://github.com/stefanbesler/struckig/actions/workflows/documentation.yml/badge.svg" alt="Documentation">
  </a>  
  <a href="https://github.com/stefanbesler/struckig/issues">
    <img src="https://img.shields.io/github/issues/stefanbesler/Struckig.svg" alt="Issues">
  </a>
  <a href="https://github.com/stefanbesler/struckig/releases">
    <img src="https://img.shields.io/github/v/release/stefanbesler/ruckig.svg?include_prereleases&sort=semver" alt="Releases">
  </a>
  <a href="https://www.gnu.org/licenses/gpl-3.0.en.html">
    <img src="https://img.shields.io/badge/license-GPLv3-green.svg" alt="GPLv3">
  </a>
</p>

This repository ports [pantor/ruckig](https://github.com/pantor/ruckig) to Structured Text to bring open-source powered Online-Trajectory-Generation to TwinCAT 3.
Only the Community Version of Ruckig is ported and pro features are not available. **Struckig itself is dual licenced, you can use the source code provided here accordingly to GPLv3. If you want to use this commercially and not disclose your own source code, Struckig is also available with a custom licence**. In the latter case, contact [me](mailto:stefan@besler.me).

# Porting progress

The original project, `ruckig` is a submodule of this repository. The commit-hash reflects the commits that are ported already - I try to keep up with changes that are done in `ruckig`.

# Unittests

This project uses [TcUnit](http://www.tcunit.org/) for unittesting. Since the library is a standalone PLC project, unittests are implemented in a different solution (subfolder `.test/Struckig/Struckig_unittest`) than the library. In order to execute the unittests the `Struckig` library has to be [saved and installed](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/4189307403.html&id=) and `TcUnit.library` has to be [downloaded](https://github.com/tcunit/TcUnit/releases) and [installed](https://infosys.beckhoff.com/english.php?content=../content/1033/tc3_plc_intro/4189333259.html&id=).

Please note, that not all unittests from the [original](https://www.github.com/pantor/ruckig) source code are ported yet, but only the `KnownExamples` and `SecondaryFeatures` tests.

# Continuous integration

Continuous integration has not really arrived in Operational technology (OT) -- yet. Some fellows from work are making good progress in implementing buildtools and preparing a CI/CD environment for TwinCAT that will be publicly available. Luckily, they agreed with me to let me try their tools as an
alpha/beta tester with this project. For more information on this topic, please contact [Zeugwerk](mailto:info@zeugwerk.at); In the meantime I
thank [@Zeugwerk](https://github.com/Zeugwerk) for letting me use their build environment in this early development stage of their DevOps tools.

# Documentation

The source code and usage documentation of this library is hosted on [https://stefanbesler.github.io/Struckig/](https://stefanbesler.github.io/Struckig/). Kudos again to [@Zeugwerk](https://github.com/Zeugwerk) for letting me beta test their TwinCAT documentation generation, which is still in alpha phase.

# Example: Create time-based profile for 1 axis

This examples shows how to create a single-axis trajectory from point A=0mm to point B=100mm.
The initial state assumes that the trajectory is in stillstand and target velocity and acceleration is set to
0. The `MinDuration` parameter is set to `10s`. Please not that `MaxVelocity`, `MaxAcceleration` and `MaxJerk` would
allow for a shorter travel time, but  if `MinDuration` together with Synchronization = SynchronizationType.TimeSync is set,
the `MinDuration` parameter is considered instead.

```
PROGRAM Example
VAR
  otg : Struckig.Otg(cycletime:=0.001, dofs:=1) := (
    EnableAutoPropagate := TRUE, //< Automatically copies the new trajectory state to the current trajectory state with every otg() call
    Synchronization :=     SynchronizationType.TimeSync, //< Set to TimeSync, otherwise MinDuration is ignored
    MinDuration :=         10.0, //< if MinDuration > 0 and Synchronization is set to TimeSync this sets the duration of the trajectory (if the other limitations would yields a shorter duration)
    MaxVelocity :=         [ 2000.0 ],
    MaxAcceleration :=     [ 20000.0 ],
    MaxJerk :=             [ 800000.0 ],
    CurrentPosition :=     [ 0.0 ],
    CurrentVelocity :=     [ 0.0 ],
    CurrentAcceleration := [ 0.0 ],
    TargetPosition :=      [ 100.0 ],
    TargetVelocity :=      [ 0.0 ],
    TargetAcceleration :=  [ 0.0 ]
  );
END_VAR

// =====================================================================================================================

otg();
// axis.SetTargetPosition(otg.NewPosition[0]); send the new position to your axis, which should be in a cyclic position mode
// axis.SetVelocityOffset(otg.NewVelocity[0]); send the new velocity to your axis, e.g. with a velocity feedforward
// axis.SetTorqueOffset( accelerationTorqueFactor * otg.NewAcceleration[0]) send the new acceleration to your acces, e.g. with a current feedforward
```

![image](https://user-images.githubusercontent.com/11271989/129452181-57d28187-cafb-44be-b1ad-f73a5ed80556.png)






