---
title: "Jetpack Compose Android"
date: 2023-02-27T13:06:38+08:00
description: "The one and only jetpack compose series"
author: "Narayan Panthi"
tags: [ "Jetpack", "Android", "Login","Signup","Nested Graph"]
theme: "light"
featured: true
cover: "https://miro.medium.com/v2/resize:fit:4800/format:webp/1*_oq8sLVW5nAhsEwyAxnA7w.jpeg"
---
![Jetpack Android](https://www.apple.com.cn/newsroom/images/product/homepod/standard/Apple-HomePod-hero-230118_big.jpg.large_2x.jpg)

🙏 Namaste!

Welcome to this comprehensive series where I’ll guide you through building a Jetpack Compose app from the ground up. This will be a quick no-nonsense guide for you, covering various sections:

The basic components of Jetpack Compose
Material3 Themes & Guidelines
Navigation with Jetpack Compose
The New Splash Screen API
Bottom Navigation in Jetpack Compose
Nested Navigation to BottomAppBar from Login Page
Display List with LazyColumn from API Network Response
Offline First, Caching with Room Database
Capturing & Uploading Images and more as we go on…

## Introduction
We Android developers spent years crafting our user interfaces using XML-based layouts. It’s time to shift gears and explore this declarative UI toolkit that promises to make our life as a developer a whole lot easier.

For those of us rooted in XML habits, the thought of embracing Jetpack Compose might seem like a leap of faith. But trust me, the benefits in terms of code clarity, development speed, and maintenance are worth it.

As this is the first part of the series, we will go through some basics and design UI with Jetpack Compose.

## Basics things to get started
In Jetpack Compose, there’s a bunch of stuff like UI Components, Layouts, and State Management. But when you’re just starting, you don’t have to understand everything at once. As you get used to designing with Compose, things like passing data and managing states will make more sense. So, don’t stress — start with the basics, and we’ll figure things out step by step.

Let’s Dive into the world of Compose

## 1. Composable Functions
In Jetpack Compose, building UIs is as simple as defining a function — except with a touch of Compose magic. All you need to do is add the `@Composable` annotation on your function, and voila! You’ve entered the world of UI creation.

```kotlin
@Composable
fun FancyButton(label: String) {
    Button(onClick = { /* Handle button click */ }) {
        Text(text = label)
    }
}
```
To preview it, like we used to write XML and preview it in the Design Tab, we need to add the following code.

```kotlin
@Preview(showBackground = true, name = "Text preview")
@Composable
fun GreetingPreview() {
    YourAppTheme {
        FancyButton(label = "Android")
    }
}
```
## Compose Layout
In Jetpack Compose, UI elements form a hierarchy, arranged by calling composable functions within one another. Think of them as invisible containers that hold your views or other layouts.

# Column
If you prefer a vertical arrangement, a Column is the way to go. It stacks your views on top of each other, creating a vertical cascade.

```kotlin
@Composable
fun UserCard(user: User) {
    Column {
        Text(text = user.profilePic)
        Text(text = user.email)
    }
}
```

### Row
For arranging views horizontally, a Row is your go-to. It lines up your elements side by side, creating a horizontal flow.

```kotlin
@Composable
fun UserCard(user: User) {
    Row {
        Text(text = user.firstName)
        Text(text = user.lastName)
    }
}
```
### Scaffold
Scaffold is a Compose function for building your app’s layout based on Material Design. It takes parameters like topBar, bottomBar, and floatingActionButton to structure key components of your application efficiently.

```kotlin
Scaffold(
    topBar = {
        TopAppBar(
            title = {
                // Placeholder for top app bar content
            }
        )
    },
    bottomBar = {
        BottomAppBar {
            // Placeholder for bottom app bar content
        }
    },
    floatingActionButton = {
        FloatingActionButton(onClick = { /* Handle click */ }) {
            Icon(Icons.Default.Add, contentDescription = "Add")
        }
    }
) { innerPadding ->
    Column() {
        // Main content
    }
}
```

# LazyColumn
Similar to RecyclerView, LazyColumn efficiently handles visible items in the viewport.

```kotlin
LazyColumn {
    items(messages) { message ->
        ProductItemRow(message)
    }
}
```
### Modifiers
Modifiers enhance Compose UI elements by providing decoration or adding behavior. Examples include backgrounds, padding, and click event listeners.
``` kotlin
Copy code
@Composable
private fun UserProfile(fullName: String) {
    Column(
        modifier = Modifier
            .padding(16.dp)
            .fillMaxWidth()
    ) {
        Text(text = "Name")
        Text(text = fullName)
    }
}
```

### TextField
A TextField is a UI component designed for users to input text or numbers.

```kotlin
@Composable
fun UserInputField() {
    var userInput by remember { mutableStateOf(TextFieldValue("")) }

    TextField(
        value = userInput,
        onValueChange = {
            userInput = it
        },
        label = { Text(text = "Enter Your Text") },
        placeholder = { Text(text = "Type something here...") },
    )
}
```

These are the basic things you need to get started, and we will learn more about them in the next part.

## III. Your First Compose Project
Let’s create our first project by selecting → New Project →Jetpack Compose Empty Activity

### Jetpack Compose Empty Activity
Let’s see the Project Structure & files created by Android Studio,

#### Color.kt — Contains color of App
The color file contains all the colors related to our app. As per Material3 Guidelines, we should auto-generate our app color theme by providing app color to the builder to follow the “Material You” styling from Material3ThemeBuilder.

#### Theme.kt — Contains style/theme of App

Our app features two primary themes based on the user's state: Light and Dark. Moreover, in alignment with “Material You” principles, the color palettes now dynamically adjust based on the selected wallpaper. To harmonize with this functionality, we need to configure the theme colors according to the chosen color palettes.

There are 26+ color roles mapped to Material Components. Explore the documentation for a comprehensive array of color options.

```
private val DarkColorScheme = darkColorScheme(
    primary = YourDarkPrimaryColor,
    secondary = YourAppDarktSecondaryColor,
    tertiary = YourAppDarkTertiaryColor
)
```

```
private val LightColorScheme = lightColorScheme(
    primary = YourAppLightPrimaryColor,
    secondary = YourAppLightSecondaryColor,
    tertiary = YourAppLightTertiaryColor

    /* Other default colors roles to override
    background = Color(0xFFFFFBFE),
    surface = Color(0xFFFFFBFE),
    onPrimary = Color.White,
    onSecondary = Color.White,
    onTertiary = Color.White,
    onBackground = Color(0xFF1C1B1F),
    onSurface = Color(0xFF1C1B1F),
    */
)
```
Material Theme will adjust your color depending on the selection.

```
val colorScheme = when {
        dynamicColor && Build.VERSION.SDK_INT >= Build.VERSION_CODES.S -> {
            val context = LocalContext.current
            if (darkTheme) dynamicDarkColorScheme(context) else dynamicLightColorScheme(context)
        }

        darkTheme -> DarkColorScheme
        else -> LightColorScheme
}
MaterialTheme(
 colorScheme = colorScheme,
 typography = Typography,
 content = content
)
```
Styling with Material3 might seem a little tricky at first, but don’t worry — it gets easier as you get the hang of it.

#### Type.kt — Contians Text Typography of App

Just like changing colors, adjusting the type means replacing the usual style of the Material theme color.

```
// Set of Material typography styles to start with
val Typography = Typography(
    bodyLarge = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Normal,
        fontSize = 16.sp,
        lineHeight = 24.sp,
        letterSpacing = 0.5.sp
    )
    /* Other default text styles to override
    labelSmall = TextStyle(
        fontFamily = FontFamily.Default,
        fontWeight = FontWeight.Medium,
        fontSize = 11.sp,
        lineHeight = 16.sp,
        letterSpacing = 0.5.sp
    )
    */
)
```


####MainActivity.kt — Main Screen of App

In Compose, an Activity still acts as the starting point for an Android app. Rather than using an XML file, as you would in the traditional View system, you utilize setContent to outline your layout. Inside it, Composable functions are called instead.

![Jetpack|inline](https://www.apple.com.cn/newsroom/images/product/homepod/standard/Apple-HomePod-2-up-230118_big.jpg.large_2x.jpg)


Let’s observe quickly what is happening here.

#### The onCreate method, where the initialization logic forMainActivity takes place.

🔵 → The setContent block sets up the Compose UI. It uses a Surface composable as a container, applying the background color from the theme. Within, the Greeting composable is invoked with the parameter "Android".

🟡 → Greeting composable function. It takes a name parameter and an optional modifier parameter with a default value of Modifier. The composable displays a Text composable with a greeting message.

🔴 → The GreetingPreview composable is a preview function that showcases the Greeting composable within the context of the TestArticleTheme. This is useful for visualizing how the UI component looks during development.

## Designing A Simple Card UI
Let’s create a TravelCard.kt file and start writing our code. We’ll learn about some UI elements.

Before we start, add these dependencies for Compose Material 3 and Coil [Image Loading Library].

```
// Material Compose
implementation "androidx.compose.material3:material3:1.1.2"

// Image Loading
implementation("io.coil-kt:coil-compose:2.5.0")
```

Simple Travel Nepal Card UI with Jetpack Compose
Think In Compose Way 🤔
The above UI can be made with the following elements.

travel_card.xml == CardView > LinearLayout > ImageView > TextView123…

Similarly, In compose we can divide them into the following sections

```
@Composable
fun TravelCard() {

    Card() {
        Column() {
            Image()
            Column() {
                Text(text = Your Category".uppercase())
                Text( text = "Your Title")
                Text(text = "Your Description")
            }
        }
    }

}
```
Now, let's make our first card by adding some modifiers,

```
@Composable
fun TravelCard() {

    Card(
        modifier = Modifier
            .padding(10.dp)
            .shadow(
                elevation = 5.dp,
                spotColor = MaterialTheme.colorScheme.secondaryContainer,
                shape = MaterialTheme.shapes.medium
            ),
        shape = MaterialTheme.shapes.medium
    ) {
        //... card contianer
      }

}
```

Then, add a Column, Image, and TextField.

```
@Composable
fun TravelCard() {

    Card(
        modifier = Modifier
            .padding(10.dp)
            .shadow(
                elevation = 5.dp,
                spotColor = MaterialTheme.colorScheme.secondaryContainer,
                shape = MaterialTheme.shapes.medium
            ),
        shape = MaterialTheme.shapes.medium
    ) {
        Column(
            Modifier
                .fillMaxWidth(),
        ) {
            Image(
                painter = painterResource(id = R.drawable.ic_travel_dummy),
                contentDescription = null,
                contentScale = ContentScale.Fit,
                modifier = Modifier
                    .padding(8.dp)
                    .height(150.dp)
                    .size(84.dp)
                    .clip(MaterialTheme.shapes.medium)
            )

            Column(
                Modifier
                    .padding(10.dp),
            ) {
                Text(
                    text = "yourText".uppercase(),
                    style = appTypography.labelSmall,
                    color = MaterialTheme.colorScheme.onSecondaryContainer,
                    modifier = Modifier.padding(8.dp)
                )

                Text(
                    text = "Your Title",
                    style = appTypography.titleLarge,
                    maxLines = 2,
                    color = MaterialTheme.colorScheme.onTertiaryContainer,
                    modifier = Modifier.padding(8.dp)
                )

                Text(
                    text = "Your Description",
                    style = appTypography.bodySmall,
                    maxLines = 3,
                    color = MaterialTheme.colorScheme.onTertiaryContainer,
                    modifier = Modifier.padding(8.dp)
                )

                Spacer(modifier = Modifier.height(8.dp))

            }
        }
    }


}

@Preview(showBackground = true)
@Composable
fun TravelCardPreview() {
    FireflyComposeTheme {
        TravelCard()
    }
}
```

You can replace the Image component with AsyncImage to load images from the URL:

```
 AsyncImage(
           model = travel.thumbnail,
           contentDescription = productEntity.title,
            modifier = Modifier
                    .background(MaterialTheme.colorScheme.secondaryContainer)
                    .fillMaxWidth()
                    .height(150.dp),
            contentScale = ContentScale.Crop,
            )
```
Finally, To display TravelCard in our app, Add the following in MainActivity.kt

```
class MainActivity : ComponentActivity() {
    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContent {
            ArticleTheme {
                Surface(
                    modifier = Modifier.fillMaxSize(),
                    color = MaterialTheme.colorScheme.background
                ) {
                    TravelCard()
                }
            }
        }
    }
}

@Preview(showBackground = true)
@Composable
fun Preview() {
    FireflyComposeTheme {
        TravelCard()
    }
}
```
Now, let's create a simple login page.

## Design Login Screen
Create a new file LoginScreen.kt and LoginComponents.kt for login page design and their components.


Login Screen — Home Screen Design from Firefly App
From the image above, there are components such as the email textfield, password textfield, SignIn button, and Signup Text. Now, let’s incorporate the email textfield component in a way that it can be utilized not only in the registration section but also across various pages of the app.

#### EmailInput & PasswordInput


```
@Composable
fun EmailInput(
    label: String,
    icon: ImageVector,
    currentValue: String,
    focusRequester: FocusRequester? = null,
    keyboardActions: KeyboardActions,
    onValueChange: (String) -> Unit
) {

    TextField(
        value = currentValue,
        onValueChange = onValueChange,
        modifier = Modifier
            .fillMaxWidth()
            .focusRequester(focusRequester ?: FocusRequester()),
        leadingIcon = { Icon(imageVector = icon, contentDescription = label) },
        label = { Text(text = label) },
        shape = Shapes.medium,
        singleLine = true,
        keyboardActions = keyboardActions,
        keyboardOptions = KeyboardOptions(
            capitalization = KeyboardCapitalization.None,
            autoCorrect = true,
            keyboardType = KeyboardType.Email,
            imeAction = ImeAction.Next
        ),
    )
}
```

```
@Composable
fun PasswordInput(
    label: String,
    icon: ImageVector,
    currentValue: String,
    focusRequester: FocusRequester? = null,
    keyboardActions: KeyboardActions,
    onValueChange: (String) -> Unit
) {

    var passwordVisible by remember { mutableStateOf(false) }

    TextField(
        value = currentValue,
        onValueChange = onValueChange,
        modifier = Modifier
            .fillMaxWidth()
            .focusRequester(focusRequester ?: FocusRequester()),
        leadingIcon = { Icon(imageVector = icon, contentDescription = label) },
        trailingIcon = {
            val passwordIcon = if (passwordVisible) {
                AppIcons.PasswordEyeVisible
            } else {
                AppIcons.PasswordEyeInvisible
            }
            val description = if (passwordVisible) {
                "Hide Password"
            } else {
                "Show Password"
            }
            IconButton(onClick = { passwordVisible = !passwordVisible }) {
                Icon(imageVector = passwordIcon, contentDescription = description)
            }
        },
        label = { Text(text = label) },
        shape = Shapes.medium,
        singleLine = true,
        keyboardActions = keyboardActions,
        visualTransformation = if (passwordVisible) {
            VisualTransformation.None
        } else {
            PasswordVisualTransformation()
        },
        keyboardOptions = KeyboardOptions(
            capitalization = KeyboardCapitalization.None,
            autoCorrect = true,
            keyboardType = KeyboardType.Password,
            imeAction = ImeAction.Next
        ),

        )
}
```

Some Parameters Info

label: String: This parameter represents the label or hint text for the email input field. It is a string that provides context or guidance to the user about the expected input.

icon: ImageVector: The icon parameter is an ImageVector that represents an icon associated with the email input field. It could be an image or a vector graphic providing a visual cue related to the email input.

For Icons, You can use this dependency, You can use directly or create a separate file for it.

```
    implementation 'androidx.compose.material:material-icons-extended:1.5.4'
object AppIcons {
    val Email = Icons.Default.Email
    val Password = Icons.Default.Lock
    val PasswordEyeVisible = Icons.Default.Visibility
    val PasswordEyeInvisible = Icons.Default.VisibilityOff
}
```
currentValue: String: This parameter holds the current value of the email input field. It represents the text that is currently entered or selected in the input field.

onValueChange: (String) -> Unit: This is a higher-order function parameter. It takes a lambda function as an argument, where the lambda function receives a String parameter. This function is a callback that is invoked when the value of the email input changes. The (String) -> Unit syntax specifies that the lambda function should take a String argument and return Unit (similar to void in other languages).

Creating a personalized login user interface is a straightforward process. Simply arrange your elements within a column and apply the desired modifiers as needed.

#### LoginScreen.kt


```
@Composable
fun LoginScreen() {

// Sperate this function as we have to addd viewmodel, declear variables here
// Identify keys actions and listener we may require for login screens.

   LoginContent(
                email = "apple@gmail.com",
                password = "password",
                onEmailChange = {
                    // listen changes of email field
                },
                onPasswordChange = {
                    // listen changes of password field
                },
                onLoginClick = {
                    // when onLogin Button is Clicked
                },
                onSignUpClick = navigateToSignUp, // signUp Click
    )
}


@Composable
fun LoginContent(
    email: String,
    password: String,
    onEmailChange: (String) -> Unit,
    onPasswordChange: (String) -> Unit,
    onLoginClick: () -> Unit,
    onSignUpClick: () -> Unit
) {
    val passwordFocusRequester = FocusRequester()
    val focusManager: FocusManager = LocalFocusManager.current

    Column(
        Modifier
            .padding(MaterialTheme.dimens.extraLarge)
            .fillMaxSize()
            .verticalScroll(rememberScrollState()),
        verticalArrangement = Arrangement.SpaceEvenly,
        horizontalAlignment = Alignment.CenterHorizontally
    ) {

        Box(
            modifier = Modifier
                .weight(2f)
                .padding(MaterialTheme.dimens.medium), contentAlignment = Alignment.Center
        ) {
            Image(
                painter = painterResource(id = R.drawable.ic_app_logo),
                contentDescription = "logo",
                Modifier.padding(10.dp)
            )
        }

        Box(
            modifier = Modifier.weight(3f),
        ) {
            Spacer(modifier = Modifier.height(20.dp))

            Column(verticalArrangement = Arrangement.Center) {
                EmailInput(
                    currentValue = email,
                    keyboardActions = KeyboardActions(onNext = { passwordFocusRequester.requestFocus() }),
                    onValueChange = onEmailChange,
                    icon = AppIcons.Email,
                    label = stringResource(id = R.string.label_email),
                )

                Spacer(modifier = Modifier.height(20.dp))

                PasswordInput(
                    currentValue = password,
                    keyboardActions = KeyboardActions(onDone = { focusManager.clearFocus() }),
                    focusRequester = passwordFocusRequester,
                    onValueChange = onPasswordChange,
                    icon = AppIcons.Password,
                    label = stringResource(id = R.string.label_password),
                )

                Spacer(modifier = Modifier.height(30.dp))

                Button(
                    onClick = {
                        onLoginClick()
                    },
                    Modifier
                        .fillMaxWidth()
                        .disableMutipleTouchEvents()
                ) {
                    Box {
                        Text(text = "Sign In", Modifier.padding(8.dp))
                    }
                }
            }
        }

        Box(
            modifier = Modifier.weight(0.5f)
        ) {
            Column(
                verticalArrangement = Arrangement.Center,
                horizontalAlignment = Alignment.CenterHorizontally
            ) {
                Row(verticalAlignment = Alignment.CenterVertically) {
                    Text(text = "Don't have an account?", color = Color.Black)
                    TextButton(onClick = {
                        onSignUpClick()
                    }) {
                        Text(text = "Sign Up")
                    }
                }
            }
        }
    }
}
```

```
@Preview(showBackground = true)
@Composable
fun DefaultPreview() {
    FireflyComposeTheme {
        LoginScreen(
            navigateToHome = {},
            navigateToSignUp = {})
    }
}
```
We’ll look into managing login button clicks, monitoring changes in the email and password inputs, and updating values in the ViewModel. First, we will learn about Navigation with Jetpack Compose and then get back to these.

For now, we’ve already set up some UI elements, and it’s quite impressive. Feel free to create one or two more UI components to familiarize yourself with the process.

Share your thoughts in the comments, and stay tuned for more tutorials on Jetpack Compose.

Thank you. Happy Reading 🐱

![Jetpack|inline](https://www.apple.com.cn/newsroom/images/product/homepod/standard/Apple-HomePod-2-up-230118_big.jpg.large_2x.jpg)
