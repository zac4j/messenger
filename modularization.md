## App Modularization Discussion 

#### 1.[Intro to App Modularization][intro_modular]
![project_arch][project_arch]
+ There kinds of Module:
	- app → Empty Application module that only launches features.
	- Feature Modules → (login, home, atmfinder, transactions, settings).
	- Base Modules → (network, auth, cache, presentation). 
+ Modules communication mechanism:
	- create differ dependency relation between modules.
```groovy
apply from: "../base-android-library.gradle"

dependencies {
    implementation project(Modules.cache)
    implementation project(Modules.network)
    implementation project(Modules.presentation)

    implementation SupportLibraries.appcompat
    implementation SupportLibraries.design
}
```

[intro_modular]:https://proandroiddev.com/intro-to-app-modularization-42411e4c421e
[project_arch]:https://cdn-images-1.medium.com/max/800/1*JiBapD4I99K4sgHVDYfXIA.png
