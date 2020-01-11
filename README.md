## machine-learning-challenge
Last Bootcamp homework assignment, December, 2019

####  From the NASA Kepler exoplanet webpage (https://exoplanetarchive.ipac.caltech.edu/docs/API_kepcandidate_columns.html)

We are looking at data from the Kepler spacecraft which was the first space mission dedicated to the search for Earth-sized 
and smaller planets in the habitable zone of other stars in our neighborhood of the galaxy. Kepler was a special-purpose spacecraft
that precisely measured the light variations from thousands of distant stars, looking for planetary transits. When a planet passes in front of its parent star, as  seen from our solar system, it blocks a small fraction of the light from that star; this is known as a transit.

#### Data Dictionary for variables stored in the CSV file

>  Kepler Object of Interest (KOI). A KOI is a target identified by the Kepler Project that displays at least 
>  one transit-like sequence within Kepler time-series photometry that appears to be of astrophysical origin and 
>  initially consistent with a planetary transit hypothesis.

> OUTPUT or "target" of analysis
* koi_disposition = category describing whether KOI is a candidate, confirmed planet, false 
positive, or not dispositioned 

> FLAGS are set when transit is not planetary in nature
>* koi_fpflag_nt = not transit like flag.  A KOI whose light curve is not consistent with that of a transiting planet. 
This includes, but is not limited to, instrumental artifacts, non-eclipsing variable stars, and spurious (very low SNR) detections.

>* koi_fpflag_ss = stellar eclipse flag.  A KOI that is observed to have a significant secondary event, transit shape, or 
out-of-eclipse variability, which indicates that the transit-like event is most likely caused by an eclipsing binary.

>* koi_fpflag_co = centroid offset flag. The source of the signal is from a nearby star.

>* koi_fpflag_ec = ephemeris match indicates contamination. The KOI shares the same period and epoch as another object and 
is judged to be the result of flux contamination in the aperture or electronic crosstalk.

> TIMES associated with the transit
>* koi_period = orbital period. The interval between consecutive planetary transits.

>* koi_time0bk = transit epoch. The time corresponding to the center of the first detected transit in Barycentric Julian Day (BJD) minus a constant offset of 2,454,833.0 days. 
The offset corresponds to 12:00 on Jan 1, 2009 UTC. Other columns have error for this parameter

> PHYSICAL parameters of star being transitted
>* koi_steff = stellar effective temperature. The photospheric temperature of the star.

>* koi_slogg = stellar surface gravity. The base-10 logarithm of the acceleration due to gravity at the surface of the star.

>* koi_srad = stellar radius. The photospheric radius of the star.

> ORBITAL geometry and location
>* ra = right ascension
>* dec = declination
>* koi_kepmap = Kepler-band

#### Feature engineering

From the column definitions, we note that the parameters which most likely indicate a exoplanet will involve size of anomaly and periodicity. Therefore, we choose the initial set of features to include the TIME, PHYSICAL, and ORBITAL parameters.
Also, include all of the flags that indicate some type of contamination to properly filter the information in the records.

selected_features = df[['koi_fpflag_nt','koi_fpflag_ss','koi_fpflag_co','koi_fpflag_ec','koi_period', 'koi_time0bk', 'koi_slogg', 'koi_steff', 'koi_srad', 'koi_kepmag', 'koi_disposition']

#### After extracting the features and target, we apply two ML algorithms.

The expectation is that the ML algorithms can evaluate the conditions and likelihood of an object being a planetary object.

Model 1:  SVM (Support Vector Machines)

Using the features as above and splitting the data into train and test, the model score for this algorithm is
Training Data Score: 0.7875262254434484
Testing Data Score: 0.7911899313501144


Model 2: Random forest

Training Data Score: 0.7428952889567042
Testing data score: 0.7397025171624714

The feature importance are in the below order, which shows that the processing flags are operating effectively:
[(0.3126964234819588, 'koi_fpflag_ss'),
 (0.27467691819368495, 'koi_fpflag_co'),
 (0.19768736162335496, 'koi_fpflag_nt'),
 (0.10143305300868043, 'koi_fpflag_ec'),
 (0.06717406754856806, 'koi_period'),
 (0.0206156753043091, 'koi_time0bk'),
 (0.014177562377243996, 'koi_steff'),
 (0.008887316642348148, 'koi_srad'),
 (0.0026067972468289693, 'koi_slogg'),
 (4.4824573022669174e-05, 'koi_kepmag')]
 
 #### Hyperparameter tuning
 
 Cycling through permutations of the training parameters to find the optimum settings resulted in about a 10% improvement
 in both model scores.
 
 In this brief analysis, the SVM model is slightly superior to the Random Forest.