orbdetpy uses JSON files to store settings and measurements for both
data simulation and orbit determination. The following sections describe
the formatting requirements for these files.

All strings in key names and values are case sensitive. Timestamps must be
in UTC and given in the ISO-8601 format "yyyy-MM-ddThh:mm:ss.ffffffZ".

Configuration Files
-------------------

Configuration files are needed for both simulation and orbit determination.

"Gravity" : Configure EGM96 gravity field {

 "Degree" : Integer in [2,360].

 "Order" : Integer in [0,degree].

 }

"OceanTides" : Configure FES2004 ocean tide model {

 "Degree" : -1 to disable ocean tides or integer in [1,100].

 "Order" : -1 to disable ocean tides or integer in [0,degree].

 }

"Drag" : Configure atmospheric density model {

 "Model" : "MSISE" for NRL MSISE-00 or "Exponential" for exponential drag.

 "MSISEFlags" : nx2 array of integers where n is in [1,23]. The first column is the flag number and the second column is the value of the corresponding MSISE flag. See <https://www.orekit.org/site-orekit-development/apidocs/org/orekit/forces/drag/atmosphere/NRLMSISE00.html> for details. All flags default to 1.

 "ExpRho0" : Density constant [kg/m^3] for exponential drag.
 
 "ExpH0": Altitude constant [m] for exponential drag.
 
 "ExpHScale" : Altitude scale factor [m] for exponential drag.

 "Coefficient" : Drag coefficient {

    "Value" : Initial value.

    "Min" : Minimum value.

    "Max" : Maximum value.

    "Estimation" : "Estimate" to estimate, "Consider" to only consider the parameter in the UKF, or anything else to do neither.
    
    }
    
 }

"SolidTides" : Configure solid tides {

 "Sun" : false to disable the Sun's contribution or true to enable.

 "Moon" : false to disable the Moon's contribution or true to enable.

 }

"ThirdBodies" : Configure third body point mass perturbations {

 "Sun" : false to disable the Sun's contribution or true to enable.
 
 "Moon" : false to disable the Moon's contribution or true to enable.

 }

"RadiationPressure" : Configure radiation pressure {

 "Sun" : false to disable the Sun's contribution or true to enable.
 
 "Creflection" : Coefficient of reflection {

  "Value" : Initial value.
 
  "Min" : Minimum value.

  "Max" : Maximum value.

  "Estimation" : "Estimate" to estimate, "Consider" to only consider the parameter in the UKF, or anything else to do neither.

  }

 }

"SpaceObject" : Details about the Resident Space Object {

 "Mass" : Object mass [kg].
    
 "Area" : Object average cross sectional area [m^2].

 }

"Propagation" : Propagation settings {

 "Start" : Epoch for InitialState; optional if InitialTLE is provided.

 "End" : End time for RSO state propagation.

 "Step" : Time step size [s]; used only for simulation runs.

 "InitialState" : Initial state vector. Units are [m] and [m/s] for position and velocity, respectively.

 "InitialTLE" : Array of 2 TLE strings which may me provided instead of InitialState.

 "InertialFrame" : Inertial reference frame to use for propagation and all state vector outputs. Valid
                   values are ["GCRF", "EME2000"]; defaults to "EME2000".

 }

"Simulation" : Optional simulation settings; not used for orbit determination runs {

 "SimulateMeasurements" : true to simulate measurements or false to only output state vectors;
                          defaults to true.

 "SkipUnobservable" : true to skip instants when the object is below the local horizon; defaults to true.

 "IncludeExtras" : true to generate additional simulated data; defaults to false.

 "IncludeStationState" : true to include station inertial coordinates; defaults to false.

 }

"Integration" : Optional tolerances for numerical integration {

 "MinTimeStep" : Minimum integration time step [s]; defaults to 1.0E-3 s.

 "MaxTimeStep" : Maximum integration time step [s]; defaults to 300.0 s.

 "AbsTolerance" : Integration absolute tolerance; defaults to 1.0E-14.

 "RelTolerance" : Integration relative tolerance; defaults to 1.0E-12.

}
 
"Stations" : Ground stations for measurements. {

 "Station_Name" : {
 
  "Latitude" : Geodetic latitude [rad].
  
  "Longitude" : Geodetic longitude [rad].
  
  "Altitude" : Height above mean sea level [m].

  "AzimuthBias" : Optional sensor bias [rad].

  "ElevationBias" : Optional sensor bias [rad].

  "RangeBias" : Optional sensor bias [m].

  "RangeRateBias" : Optional sensor bias [m/s].

  "RightAscensionBias" : Optional sensor bias [rad].

  "DeclinationBias" : Optional sensor bias [rad].

  "PositionBias" : Optional sensor bias [m, m, m].

  "PositionVelocityBias" : Optional sensor bias [m, m, m, m/s, m/s, m/s].

  "BiasEstimation" : (UKF only) "Estimate" to estimate, "Consider" to only consider biases, or anything else to do neither.

  }
  
 }

"Maneuvers" : One or more maneuvers to include during simulation or less commonly during orbit determination [

 {
 
  "TriggerEvent" : "DateTime" to trigger at the specified time instant.
                   "LongitudeCrossing" to detect when the given geodetic longitude is crossed.

  "TriggerParams" : Not required for "DateTime".
  		    [geodetic_longitude_radians] for "LongitudeCrossing" detection.

  "Time" : Time string for maneuver; required only when TriggerEvent is "DateTime".

  "ManeuverType" : One of ["ConstantThrust", "NorthSouthStationing", "EastWestStationing", "SemiMajorAxisChange",
                   "PerigeeChange", "EccentricityChange", "InclinationChange", "RAANChange", "ArgPerigeeChange",
		   "StopPropagation"].

  "ManeuverParams" : [dir_x, dir_y, dir_z, duration_seconds, thrust_Newtons, Isp_seconds] for "ConstantThrust".
    		     [target_geodetic_latitude, deltav_interval, deltav_count] for "NorthSouthStationing".
                     [target_geodetic_longitude, deltav_interval, deltav_count] for "EastWestStationing".
		     [target_SMA, deltav_interval, deltav_count] for "SemiMajorAxisChange".
                     [target_perigee_radius, deltav_interval, deltav_count] for "PerigeeChange".
		     [target_eccentricity, deltav_interval, deltav_count] for "EccentricityChange".
		     [target_inclination, deltav_interval, deltav_count] for "InclinationChange".
		     [target_RAAN, deltav_interval, deltav_count] for "RAANChange".
		     [target_perigee_argument, deltav_interval, deltav_count] for "ArgPerigeeChange".
		     Not required for "StopPropagation".

 }
 
 ]

"Measurements" : Configure input measurements for orbit determination or output measurements from simulated data {

 "Range" : {

  "TwoWay" : true or false.

  "Error" : Theoretical measurement error [m].
  
 }

 "RangeRate" : {

  "TwoWay" : true or false.

  "Error" : Theoretical measurement error [m/s].

 }

 "Azimuth" : {

  "Error" : Theoretical measurement error [rad].

 }

 "Elevation" : {

  "Error" : Theoretical measurement error [rad].

 }

 "RightAscension" : {

  "Error" : Theoretical measurement error [rad].

 }

 "Declination" : {

  "Error" : Theoretical measurement error [rad].

 }

 "Position" : {

  "Error" : Theoretical measurement error [m, m, m].

 }

 "PositionVelocity" : {

  "Error" : Theoretical measurement error [m, m, m, m/s, m/s, m/s].

 }
 
 }

Valid combinations of measurements are as follows:

1. Range
2. RangeRate
3. Range + RangeRate
4. Azimuth + Elevation
5. RightAscension + Declination
6. Position
7. PositionVelocity
 
"Estimation" : Configure parameters for estimation filters {

 "Filter" : Must be either "UKF" or "EKF".

 "Covariance" : Diagonal elements of covariance matrix with dimension 6 plus number of estimated parameters.

 "ProcessNoise" : Diagonal elements of process noise matrix with dimension 6. Not used when DMC is in effect.

 "DMCCorrTime" : DMC correlation time. Setting this to zero disables DMC.

 "DMCSigmaPert" : Sigma for DMC acceleration. Setting this to zero disables DMC.

 "DMCAcceleration" : DMC acceleration bounds {
 
    "Value" : Initial value [m/s^2].
    
    "Min" : Minimum value [m/s^2].
    
    "Max" : Maximum value [m/s^2].
    
    }

 "OutlierSigma" : Number of sigmas from innovation for a measurement to be an outlier; positive number enables outlier detection.

 "OutlierWarmup" : Number of measurements to process before enabling outlier detection; positive number enables outlier detection.

 }

Input Files
-----------

Only orbit determination requires input (measurement) files, which must
have the following structure. Each entry in the array corresponds to the
measurement(s) taken at a particular time instant and must conform to the
valid combinations listed  above.

[

 {
 
  "Time" : Measurement time stamp
  
  "Station" : Ground station name(s) from the configuration file's "Stations" array.
  
  "Range" : Optional based on measurements configured in "Measurements" [m].
  
  "RangeRate" : Optional based on measurements configured in "Measurements" [m/s].

  "Azimuth" : Optional based on measurements configured in "Measurements" [rad].

  "Elevation" : Optional based on measurements configured in "Measurements" [rad].

  "RightAscension" : Optional based on measurements configured in "Measurements" [rad].

  "Declination" : Optional based on measurements configured in "Measurements" [rad].

  "Position" : Optional based on measurements configured in "Measurements" [m, m, m].

  "PositionVelocity" : Optional based on measurements configured in "Measurements" [m, m, m, m/s, m/s, m/s].

 }

]
