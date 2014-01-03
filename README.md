iOS Coding Standards
====================

This is heavily influenced by the New York Times' Styleguide (https://github.com/NYTimes/objective-c-style-guide). A few additions and changes have been made to suit my opinions and style, but credit is due to them for a reasonably complete standard. You should also look there for a nice set of links to Apple standards documents.

Let's get right to it.

###Use Dot Notation for Properties
This makes it clear to the reader that you're using a property instead of a method. Always use bracket notation for method access.

    //do this
    view.backgroundColor = [UIColor orangeColor];
    [UIApplication sharedApplication].delegate;

    //not this
    [view setBackgroundColor:[UIColor orangeColor]];
    UIApplication.sharedApplication.delegate;

###Declare Properties Instead of Creating Instance Variables
And certainly don't declare the instance variables that will back the properties, as they will be generated for you automatically.

###Declare Private Properties In Class Extensions (Anonymous Categories)
There is a related pattern of declaring a property read-only in the public interface then re-declaring it readwrite in the class extension.

###Name Properties and Variables Camel-Case
Start with a lowercase letter, then jam the words together, don't use underscores in the middle of the words.

###AutoSynthesize Properties Whenever Possible
When you can't, create the backing variable with an underscore prefix just like the compiler does

    //example
    @synthesize descriptiveVariableName = _descriptiveVariableName;

###Only Access Instance Variables in Init and Dealloc Methods Or In Custom Accessor Methods
The compiler creates an instance variable for all auto-synthesized properties that is the property name prefixed by an underscore. In a custom accessor, you must of course use the instance variable since you are defining how the dot notation will work. In Init and Dealloc methods, Apple has specified that the synthesized accessors could be in an unknown state, so you should use the instance variables to access there too. See: https://developer.apple.com/library/mac/documentation/Cocoa/Conceptual/MemoryMgmt/Articles/mmPractical.html#//apple_ref/doc/uid/TP40004447-SW6

###Do Not Prefix Other Variables or Methods with "_"
Let that notation have the special meaning of instance variables that correspond with a property. Apple reserves methods beginning with "_" for their own use, so you should not name any methods this way.

###4 Spaces for Tab Setting
This is an Xcode setting, in Preferences -> Text Editing -> Indentation, set "Prefer indent using:" to "spaces" and make sure Tab width and Indent width are both "4" spaces.

###Curly Braces
Generally favor Allman-style braces. See http://en.wikipedia.org/wiki/Indent_style#Allman_style for how this looks. This basically means opening "{" on a new line, at the same indent level as the matching closing "}". An exception is made for blocks because, among other things, it gets awkward when they're arguments to a method and it looks silly to have a "^" and then a newline for void blocks.

    //if clause example
    if (weHaveTacos)
    {
        [self eatThem];
    }
    else if (weHaveBurritosInFridge)
    {
        [self openFridgeAndEatBurritos];
    }
    else
    {
        [self panic];
    }


    //block example
    __weak MyViewController *weakSelf = self;
    [[RKObjectManager sharedManager]
     getObjectsAtPath:@"/1/tacos"
     parameters:nil
     success:^(RKObjectRequestOperation *operation, RKMappingResult *mappingResult) {
         __strong MyViewController *strongSelf = weakSelf;
         if (! strongSelf)
         {
             return;
         }
         
         strongSelf.allTacos = [mappingResult array];
         [strongSelf.tableView reloadData];
     }
     
     failure:^(RKObjectRequestOperation *operation, NSError *error) {
         NSLog(@"error fetching lunch");
     }];

###Asterisks for Pointers
Stick them to the variable, not to the type or spaced in between. Between is OK for constants, though.

    //do this
    NSString *aString;

    //not this
    NSString * aString;

    //or this
    NSString* aString;

    //this has to be OK for constants, since you have to have "const" between the type and the name
    NSString * const TACODefaultsKeyLastUpdated = @"lastUpdatedKey";

###Blank lines
Put a single blank line between methods. Use blank lines to clearly separate ideas in methods, especially before comments.

###Conditionals
Always use curly braces, even if the expression would compile without them.

    //do this
    if (!error)
    {
        return success;
    }

    //not this
    if (!error)
        return success;

    //and also not this
    if (!error) return success;

###Method Signatures
There should only be a space after the scope (+/-) symbol and a space between each argument to the method, like this:

    - (NSString *)stringByReplacingOccurrencesOfString:(NSString *)findString withString:(NSString *)replacementString;

###Variable Names
Descriptive names are preferred, single letters or very short abbreviations are tolerated for for() loops or other tight loops.

    //do this
    UIButton *settingsButton;
    UILabel *nameLabel;

    //not this
    UIButton *setBut;
    UILabel *lbl;

###Namespace Prefixing

A three- or four-letter prefix based on a project name, ("TACO" for example) should always be used for class names and constants, however may be omitted for Core Data entity names. Constants should be camel-case with all words capitalized and prefixed by the related class name for clarity.

    //do this
    static const NSTimeInterval TACOMainViewControllerFadeAnimationDuration = 0.3;

    //not this
    static const NSTimeInterval fadetime = 0.3;

###Category Naming
To make it clear which categories are a project's extensions to Apple classes, name the category files like "Classname+TACO.h”.

To avoid method name collisions, Apple recommends prefixing methods in categories you create on built-in Apple classes. You can use the same prefix as for your classes, lowercase, then an underscore, then the method name.

    #import "UIColor+TACO".h

    @implementation UIColor (TACO)
    + (UIColor *) taco_textColor
    {
        return [UIColor colorWithRed:58.0/255.0 green:58.0f/255.0 blue:58.0f/255.0 alpha:1.0f];
    }

###Comments
Write a comment, when needed, to explain WHY a piece of code does something. Like stale code, make sure to remove stale comments during code maintenance. Favor single line style comments ('//') except when doing documentation.

Comment all header files using HeaderDoc. There are Xcode snippets below to help with this. Xcode 5 automatically generates inline documentation (contextual help) for your classes and methods if you do this. What's more, if you need to generate a stand-alone API document, the tool exists on every development Mac to do this (headerdoc2html).

Remove the default attribution headers generated by Xcode. The copyright information is often inaccurate anyway. An Xcode snippet for a simplified file banner is included below.

###Implementation File Separators
Separate groups of methods with pragma mark calls and corresponding comment marks using the Xcode snippet included below.

###Organize Sections of Class Implementation Files Consistently
It is helpful if things are located in the same place every time, so here's a rough outline of a *.m file:

* imports
* constants
* class extension (anonymous category)
* synthesize calls (if even needed, usually not)
* init/dealloc methods
* accessors
* methods defined in public interface
* view lifecycle methods (for view controllers)
* protocol methods (grouped per protocol)
* private methods

###Modern Objective C Literals
Favor the modern syntax for NSNumbers, NSArrays, and NSDictionaries over the older class methods like "dictionaryWithObjectsAndKeys".

###CGRect Access
Favor the convenience methods Apple gives over fishing into the structures yourself

    //with this...
    CGRect frame = self.view.frame;

    //do this
    CGFloat x = CGRectGetMinX(frame);
    CGFloat y = CGRectGetMinY(frame);
    CGFloat width = CGRectGetWidth(frame);
    CGFloat height = CGRectGetHeight(frame);

    //not this
    CGFloat x = frame.origin.x;
    CGFloat y = frame.origin.y;
    CGFloat width = frame.size.width;
    CGFloat height = frame.size.height;

###Use Constants For "Magic Numbers" and Strings
In-line string and number literals are hard to locate and change, so you should break these out to the top of the implementation file. Prefer constants over defines.

    //do this
    static NSString * const TACOAboutViewControllerLocationName = NSLocalizedString(@"Seattle Office, @"Seattle Office");
    static const CGFloat TACOImageThumbnailHeight = 50.0f;

    //not this
    #define LocationName @“Seattle Office"
    #define ThumbnailHeight 50

###Use Modern Objective-C Enums
Apple provides a macro, NS_ENUM, to help you write this correctly. When done right, you get type checking and compiler warnings when values are left out of case blocks.

    //example
    typedef NS_ENUM(NSInteger, TACOMainTableViewControllerSection)
    {
        TACOMainTableViewControllerSectionTop,
        TACOMainTableViewControllerSectionMiddle,
        TACOMainTableViewControllerSectionBottom,
        TACOMainTableViewControllerSectionCount
    };

###Singletons
Use the Xcode snippet provided below to do singletons consistently. The dispatch_once() call is crucial to this working correctly.

###Helpful Xcode Snippets
Using Xcode snippets with completion shortcuts defined can make consistent formatting and patterns possible within a project. Consider adding these to Xcode:

**Source Code Header**

    /******************************************************************************
     *
     * <#Filename Goes Here#>
     *
     ******************************************************************************/

**Code Separator**

    //------------------------------------------------------------------------------
    #pragma mark - <#name this code section#>
    //------------------------------------------------------------------------------

**HeaderDoc Class**

    /*!
     @class <#class name#>
     @brief <#brief description#>
     @discussion <#detailed description#>
     */

**HeaderDoc Method**

    /*!
     @brief <#brief description#>
     @param <#parameter name#> <#parameter description#>
     @return <#describe what is returned#>
     */

**HeaderDoc One-Line**

    /*! @brief <#description of method or property#> */

**Singleton Method**

    + (instancetype)sharedInstance
    {
        static <#class#> *sharedInstance = nil;
        static dispatch_once_t onceToken;
        
        dispatch_once(&onceToken, ^{
            sharedInstance = [[<#class#> alloc] init];
        });
        return sharedInstance;
    }

**Main Thread Block**

    dispatch_async(dispatch_get_main_queue(), ^{
        <#block code here#>
    });

**Weak Self**

    __weak typeof(self) *weakSelf = self;

**Strong Self**

    __strong typeof(self) *strongSelf = weakSelf;
    if (! strongSelf)
    {
        return;
    }
