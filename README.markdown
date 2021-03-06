Behavior Driven Legacy Testing
===============================

**Why would you want to test a legacy system, such as a classic Asp application?**

One reason would be so that you could modify it safely.  A BDD approach to legacy maintenance also lets 
you rewrite a system piece by piece.  In many instances the *system is the spec*. This means you would do well
to document whatever you touch since you usually own that code afterwards.  BDD provides the best form of
documentation I know of in the form of *executable documentation*.  

*What is the benefit of [executable documentation?](http://www.literateprogramming.com/index.html)*

Well with executable documentation you could [cut and paste it](http://www.literateprogramming.com/quotes_ad.html) 
in an email and send it to a business expert.  This means that there would be less 
opportunity for something to get lost in the translation between your expert's statements 
and the implementation of those statements in code.

Take a look at this example:

``` ruby

Feature: Login
  In order to log into my app
  As my app's personnel
  I want to put in my user name and password
  
  Scenario: Log in
    Given I enter my username
    And I enter my password
    When I press Continue
    Then I should see "Welcome" 
```

Even without any [further explanation](https://github.com/cucumber/cucumber/wiki/Gherkin), 
(if you know english) you should be able to read and understand the above statements very,
very, easily.  This means your business experts can *at least be able to read* your code.
And yes, that is code.  Therefore, **a feature is executable documentation**


**Requirements**
  
You must have [ruby](http://www.ruby-lang.org/en/downloads/) and [rubygems](http://rubyforge.org/frs/?group_id=126)
The sql server helpers in this gem use the win32ole which is only available on windows.  Everything else will
work on mac or linux.

**Installation**

```
gem install bdd-legacy
```

Then run the bdd-legacy command for your app.  This command will 
create a directory with a cucumber test suite with a number of 
utilities to get you started.

```
bdd-legacy mylegacyapp
```
**Specifiy Url**

Edit the *env.rb* file and point it to your application's domain name and url
Note: this can be a non-local url

``` ruby
#/features/support/env.rb
# Change this to the domain of your web server.  Don't add the full url
$workingAppHostLink='http://localhost'
# add the url of your login page
$workingAppLoginRoute='/welcome/logon.asp'
```

**Specify login steps**

If you have a log in page, edit your the **login_steps.rb** file and change the **fill in** function 
to be the *html* name/id of your username and password **fields** on your log in page

``` ruby
#/features/step_definitions/login_steps.rb
Given /^I enter my username$/ do
    fill_in 'Username', :with => $workingAppUser
end

Given /^I enter my password$/ do
  fill_in 'Password', :with => $workingAppPW
end
```
**Username/Password**

Change the application's user id and password in your *env.rb* file.
These will get put into the fields previously mentioned

``` ruby
#/features/support/env.rb
$workingAppUser='myapplicationusername'
$workingAppPW='myapplicationpw'
```
**Work flow**

Your normal workflow will start with writing a feature with scenarios similar to the following:

```ruby
Feature: Home Page
  In order to work in my app
  As my apps personnel
  I need to land on the home page
  
  Scenario: Land on the home page
    Given I am logged in
    Then I should see "My Apps greeting"    
    And I should see "Something else that I want to check"
````
The feature relates to something the user wants to be able to do in your application.
The Scenario actually manipulates your application.
If you were to add the above feature into a 'homepage.feature' file you could
then run the following command:

````
cucumber features
````

which would then give you the following output:

``` ruby
Given /^I am logged in to my app$/ do
  pending # express the regexp above with the code you wish you had
end

Then /^I should see something like "([^"]*)"$/ do |arg1|
  pending # express the regexp above with the code you wish you had
end

When /^I press Continue$/ do
  pending # express the regexp above with the code you wish you had
end
```

Which you then cut and paste into a step definition file.  
Something like \features\step_definitions\home_page_steps.rb would work

**Step Definitions**

In the step definitions file, you will be using [Capybara](https://github.com/jnicklas/capybara)
commands to call your legacy application. The capybara command for one of the 
previous steps looks like this:

``` ruby
Then /^I should see something like "([^"]*)"$/ do |arg1|
  page.should have_content(arg1)
end  
```

You can find examples of the capybara commands by looking at the specs in the driver.rb file.
To find the file run the following command

``` ruby
gem which capybara
```

which will give you the capybara directory on your machine.  The driver.rb file will be 
in (directory)\lib\capybara\spec

**Complete workflow**

1. Defining a feature (or scenario)
2. Copying and pasting the generated scenario matcher into a step definition file
3. Adding the (capybara) code to the step definition
4. Running the test with the 'cucumber features' command until the tests are green
5. Start over

**Firefox vs Internet Explorer**

The firefox webdriver is fast compared to Internet Explorer so I would recommend you use firefox for your testing.  
In some cases if you are on windows you may want to test your application in IE (maybe your application only works in IE).
In order to this you can use a tag.  Using an IE tag on a feature is done like so:

``` ruby
@ie
Scenario: Log in
  Given I enter my username
  And I enter my password
  When I press "Continue"
  Then I should see the environment link 
  And I select the environment link
```

Firefox is done the same way. 

``` ruby 
@firefox
Scenario: Log in
  Given I enter my username
  And I enter my password
  When I press "Continue"
  Then I should see the environment link 
  And I select the environment link
```

When running cucumber you can specify to run features for ie or firefox (or both)

```
cucumber features --tags @firefox
cucumber features --tags @ie
cucumber features --tags @ie,@firefox
```



