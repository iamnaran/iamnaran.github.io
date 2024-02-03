---
title: 'Multiple Back Stack Navigation in Jetpack Compose'
date: '2024-01-17T13:06:38+08:00'
description: 'A perfect professional guide for Nested Navigation that supports Multiple BackStacks'
author: 'Narayan Panthi'
cover: 'https://cdn-images-1.medium.com/max/1200/1*wfg1qr9XPKWTYentA2QRSQ.jpeg'
tags: ["Android", "Mutiple BackStack Navigation", "Jetpack Compose", "Nested Navigation"]
theme: 'light'
---

![](https://cdn-images-1.medium.com/max/1200/1*wfg1qr9XPKWTYentA2QRSQ.jpeg)

# Nested Navigation with Bottom Navigation that supports Mutiple Back Stack.

💥 Hello, In this article we are going to implement a nested  **navigation with multiple back stack.**
We will learn how exactly nested  **navigation** are made.


**Just Give me all the Code 👇**

[Github Link](https://gist.github.com/iamnaran/9532569e335cd346412e61b5dbe6feda)

Certainly! Imagine building an app with a login feature and a bottom navigation menu. In such a scenario, nested navigation is essential. Why? Because we want two start destinations: first, show the login page when the user opens the app, and after logging in, display the bottom navigation page. The bottom navigation itself has its start destinations like "Home," "Notification," and "Profile."

To make this work smoothly, we employ a nested navigation host, equipped with its navigation controller, facilitating seamless navigation between different composables.

I hope this clarifies things! Feel free to ask if you have any questions.


## Let's Get Started,

# Step 1 — Create a AuthNavGraph.kt

This is our initial navigation graph, encompassing all the steps before authentication, such as LoginScreen, Registration Steps, Forgot Password, and more.

```
fun NavGraphBuilder.authNavGraph(navController: NavHostController) {

    navigation(
        startDestination = AppScreen.Auth.Login.route,
        route = AppScreen.Auth.route
    ) {
        composable(route = AppScreen.Auth.Login.route) {
            LoginScreen(
                navigateToHome = {
                    navController.navigate(AppScreen.Main.route) {
                        popUpTo(AppScreen.Auth.route) {
                            inclusive = true
                        }
                    }
                },
                navigateToSignUp = {
                    navController.navigate(AppScreen.Auth.Register.route)
                },
            )
        }

        composable(route = AppScreen.Auth.Register.route) {
            SignUpScreen(onNavigateBack = {
                navController.navigateUp()
            })
        }
    }

}

```


# Step 2 — Create a BottomBar.kt

This represents our bottom navigation bar, including distinct screens for Home, Notifications, and Profile functionalities.

```
@Composable
fun BottomBar(
    navController: NavHostController,
) {
    val navigationScreen = listOf(
        AppScreen.Main.Home, AppScreen.Main.Notification, AppScreen.Main.Profile
    )

    NavigationBar {

        val navBackStackEntry by navController.currentBackStackEntryAsState()
        val currentRoute = navBackStackEntry?.destination?.route

        navigationScreen.forEach { item ->

            NavigationBarItem(
                selected = currentRoute == item.route,

                label = {
                    Text(text = stringResource(id = item.title!!), style = MaterialTheme.typography.displaySmall)
                },
                icon = {
//                    BadgedBox(badge = {}) {
//
//                    }
                    Icon(
                        imageVector = (if (item.route == currentRoute) item.selectedIcon else item.unselectedIcon)!!,
                        contentDescription = stringResource(id = item.title!!)
                    )
                },

                onClick = {
                    navController.navigate(item.route) {
                        popUpTo(navController.graph.findStartDestination().id) {
                            saveState = true
                        }
                        launchSingleTop = true
                        restoreState = true
                    }
                },
            )
        }
    }
}

```


# Step 3 — Create a ChildNavHost.kt

This serves as our navigation host, accommodating multiple back stacks for each specific item in the bottom navigation, such as the Home Nav Host, Notification Nav Host and Profile Nav Host

```
@Composable
fun HomeNavHost() {

    val homeNavController = rememberNavController()
    NavHost(navController = homeNavController, startDestination = AppScreen.Main.Home.route){

        composable(
            route = AppScreen.Main.Home.route) {
            HomeScreen(onProductClick = {
                val route = AppScreen.Main.ProductDetail.createRoute(productId = it)
                AppLog.showLog("Router---> $route")
                homeNavController.navigate(route)
            })
        }

        composable(route = AppScreen.Main.ProductDetail.route) {
            ProductDetail() {
                homeNavController.navigateUp()
            }
        }

    }

}

--- Profile Nav Host

@Composable
fun ProfileNavHost(rootNavController: NavHostController) {
    val profileNavController = rememberNavController()

    NavHost(navController = profileNavController, startDestination = AppScreen.Main.Profile.route) {

        composable(
            route = AppScreen.Main.Profile.route
        ) {
            ProfileScreen(navigateToLogin = {
                rootNavController.navigate(AppScreen.Auth.route) {
                    popUpTo(AppScreen.Main.route) {
                        inclusive = true
                    }
                }
            })
        }

    }

}

--- Notification Nav Host

fun NotificationNavHost() {
    val profileNavController = rememberNavController()

    NavHost(navController = profileNavController, startDestination = AppScreen.Main.Notification.route){

        composable(
            route = AppScreen.Main.Notification.route
        ) {
            NotificationScreen()
        }

    }

}


```


# Step 4 — Create a MainNavGraph.kt

It consit our navigation for main bottom navigation

```
fun NavGraphBuilder.mainNavGraph(rootNavController: NavHostController) {

    navigation(
        route = AppScreen.Main.route,
        startDestination = AppScreen.Main.Home.route
    ) {

        composable(route = AppScreen.Main.Home.route) {
            HomeNavHost()
        }

        composable(route = AppScreen.Main.Notification.route) {
            NotificationNavHost()
        }

        composable(route = AppScreen.Main.Profile.route) {
            ProfileNavHost(rootNavController)
        }

    }

}
```


# Step 5 — Create a RootNavHost.kt

Defining the navigation structure for of our Navigation. which enables nested navigations

```
@Composable
fun RootNavHost(isLoggedIn: Boolean, rootNavHostController: NavHostController) {
    NavHost(
        navController = rootNavHostController,
        startDestination = if(isLoggedIn) AppScreen.Main.route else AppScreen.Auth.route
    ) {

        authNavGraph(rootNavHostController)
        mainNavGraph(rootNavHostController)
    }
}
```

# Step 6 — Create a MainActivity.kt

Finally, we can create MainActivity where we well add RootMultipleBackStackNavHost in our Scaffold contains

```
 // main activity components ...
  Scaffold(
                    snackbarHost = {
                        SnackbarHost(hostState = snackbarHostState)
                    },
                    bottomBar = {
                        if (bottomBarState.value) {
                            BottomBar(navController = navController)
                        }
                    }) { paddingValues ->
                    Box(
                        modifier = Modifier.padding(paddingValues)
                    ) {

                        // For multiple back stack nav host
                        RootMultipleBackStackNavHost(
                            isAuthenticated,
                            rootNavHostController = navController
                        )
                    }
                }
```


Hence, we can use this techniqmique to add mutiple backstack with nested navigation

Thank you… Goodbye....
Happy Readings....



