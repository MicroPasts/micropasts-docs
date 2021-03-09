# Project Tutorial

This tutorial uses the demo project **Flickr Person** ([source
code](https://github.com/Scifabric/app-flickrperson)) built by Scifabric for PYBOSSA. This demo is a simple microtasking project where users have to answer the following question: *Do you see a human face in this photo?* The possible answers are: *Yes, No* and *I don't know*. In other words, this is an example of a simple crowdsourcing project for image classification.

The demo project Flickr Person has two main components:

- A Python script that creates the tasks in
  PYBOSSA using the Flickr API, and
- the task-presenter: an HTML + Javascript structure that will show
  the tasks to the users and save their answers.

This tutorial uses the PYBOSSA [pbs command line tool](pbs.md) as it will show you how you can handle your project from the command line like a pro.

## Setting Things Up

To run the tutorial, you will need to create an account in a
PYBOSSA server. The PYBOSSA server could be running on your computer or in a third party server.

Once you have a PYBOSSA account, you will have access to your profile by clicking on your name, and then on the **My Settings** section. There, you will find your API-KEY.

![image](https://i.imgur.com/JcxciZc.png)

This **API-KEY** will identify and authenticate you via the PYBOSSA API. It will allow you to create a project, add tasks, update the project, etc. As you will be the owner of the project, only you will be able to perform these actions, but anyone will be able to participate in your project.

!!! note
    This tutorial uses the [pbs command line tool](pbs.md). You need to install it in your system before proceeding. Please, check the [pbs documentation for more information](pbs.md).
   
## Creating the Project

There are two possible methods for creating a project:

- web-interface: click on your username, and you will see a section
  named **projects** list. In that section, you will be able to create a project using the web interface.
- API-interface: using the **pbs** command line tool.

For this tutorial, we are going to use the second option. The reason is that via the API you will have more flexibility than via the web interface.

Therefore, for creating the project, you will need two parameters:

- the URL of the PYBOSSA server, and
- an API-KEY to authenticate yourself in the PYBOSSA server.

!!! tip
    If you are running a PYBOSSA server locally, you can omit the URL parameter as by default it uses the URL http://localhost:5000.


### Getting the project's source code

Now that we know where are we going to create the project (the server URL) and that we have an API key,  we can download the source code of the project. To get the code, we will clone the [Flickr
Person Finder Repository](http://github.com/Scifabric/app-flickrperson). This step will download all the code and scripts to your computer.

![image](https://i.imgur.com/CYPnPft.png)

To clone the code, we will use Git. Git is a free and open source distributed version control system designed to handle everything from small to very large projects with speed and efficiency. Git is easy to learn and has a tiny footprint with lightning fast performance.

If you are new to Git, we recommend you to take
this [free and on-line course](http://try.github.com) (it will take you
only 15 minutes!) where you will learn the basics, which are the main
concepts that you will need for cloning the demo project repository.

If you prefer to skip the course and take it in a later stage, the
commands that you need to clone the repository are:

```bash
git clone git://github.com/Scifabric/app-flickrperson.git
```

After running that command,  a new folder named **app-flickrperson** will be created from where you run the command.

### Configuring the name, short name, thumbnail, etc.

The Flickr Person Finder provides a file called [project.json](https://github.com/Scifabric/app-flickrperson/blob/master/project.json) that has the following content:

```javascript
{
    "name": "Flickr Person Finder",
    "short_name": "flickrperson",
    "description": "Image pattern recognition",
}
```

This file, project.json identifies your project. It has its name, as well as a short description about it. As we are creating a new project, please, modify the **name** and **short_name** to make it yours.

!!! warning
    The **name** and **short_name** of the project **must be unique**! Otherwise, you will get an error (IntegrityError) when creating the project.


**Description** will be the text shown in the project listing page. It's important that you try to have a short description that explains what your project does.

Now that we have the **project.json** file ready, we can create the
project:

``` bash
pbs --server server --api-key key create_project
```

This command will read the values from the **project.json** file and use them to create a draft project in the PYBOSSA server of your
choice.

!!! note
    You can save some typing if you create a config file for pbs. Please,
    check the [pbs page](pbs.md#configuring-pbs) for more details.

If you want to check if the project exists, just open your web browser,
and type in the following URL http://server/project/short_name

Where **short_name** is the value of the key with the same name in the file: **project.json**. You should get a project page. Now, let's add some tasks to the project.

### Providing more details about the project

Up to now we have created the project, added some tasks, but the project still lacks a lot of information. For example, a welcome page (or long description) of the project, so the users can know what this project is about.

If you check the source code, you will see that there is a file named
*long_description.md*. This file has a lengthy description of the project, explaining different aspects of it.

This information is not mandatory. However it will be beneficial for
the users as they will get a bit more of information about the project
goals.

The file can be composed using Markdown or plain text.

The long description will be shown on the project home page:

    https://yourserver/project/flickrperson

If you want to modify the description you have two options, edit it via the web interface, or change locally the *long_description.md* file and run pbs to update it:

``` bash
pbs update_project
```

### Adding an icon to the project
It is possible also to add a nice icon for the project. By default
PYBOSSA will render a 100x100 pixels empty thumbnail for those projects that do not provide it.

If you want to add an icon you can do it by using the web interface.
Just go to the **Settings** tab within your project. There, select the
image file you want to use and push the **Upload** button. That's all!

### Protecting the project with a password
If for any reason, you want to allow only certain people to contribute
to your project, you can set a password. Thus, every time a user (either anonymous or authenticated) wants to contribute to the project, it will be asked to introduce the password. The user will then be able to contribute to the project for 30 minutes (this is a value by default, can be changed in every PYBOSSA server). After this time, the user will be asked again to introduce the password if the user wants to continue contributing, and so on.


## Adding tasks to the project

Now that we have the project created, we can add some tasks to it. PYBOSSA will deliver the tasks for the users (authenticated and
anonymous ones) and store the submitted answers in the PYBOSSA database so that you can process them in a later stage.

A PYBOSSA task is a JSON object with the information that needs to be processed by the volunteers. Usually, it will be a link to a media file (image, video, sound clip, PDF file, etc.) that needs to be processed.

**PYBOSSA does not store any data; it only links data in the tasks**. This feature is really cool as you will always have control of the data. 

While PYBOSSA internally uses JSON for storing the data, you can add  tasks to your project using several formats:

* CSV: a comma-separated spreadsheet-
* Excel: xlsx from 2010. It imports the first sheet).
* JSON: a lightweight data-interchange format.
* PO (any po file that you want to translate).
* PROPERTIES (any PROPERTIES file that you want to translate).

The demo project comes with a CSV sample file, which has the following structure:

| question | url_m | link | url_b |
| -------- | ----- | ---- | ----- |
| Do you see a human face in this photo?| http://srv/img_m.jpg | http://srv/img | http://srv/img_b.jpg |

Additionally there is a script named: **get_images.py** that will
contact Flickr, get the latest published photos to this web service, and
save them in JSON format as a file (flickr_tasks.json), with the same
structure as the CSV file (the keys are the same):

``` javascript
{ 'link': 'http://www.flickr.com/photos/teleyinex/2945647308/',
  'url_m': 'http://farm4.staticflickr.com/3208/2945647308_f048cc1633_m.jpg', 
  'url_b': 'http://farm4.staticflickr.com/3208/2945647308_f048cc1633_b.jpg' }
```

!!! note
    Flickr creates from the original image different cropped versions of the image. It uses a pattern to distinguish them: **_m** for medium size, and **_b** for the big ones. There are more options, so if you need more help in this matter, check the official [Flickr    documentation](http://www.flickr.com/services/api/).

As we have a CSV file with some tasks, let's use it for adding some
tasks to your project. For adding tasks using the CSV file,  all you have to do is the following:

``` bash
pbs add_tasks --tasks-file flickr_tasks.csv
```

After running this command, you will see a progress bar that will let
you know when all the tasks have been added to your project. This has been really easy, right? As you can see, adding tasks to a project is really straightforward if you have a CSV or Excel file. Each row will become a task in PYBOSSA, and you only have to run one command to get all of them into your project.

As a bonus, let's also add some tasks using the **get_images.py** script. This script will contact Flickr, get the last 20 published photos, and then, save them in JSON format into a file called **flickr_tasks.json**.  By doing this, we are showing you how you can easily extract data from third-party services and import them into a PYBOSSA project. Thus, let's start by running the command and getting the tasks:

``` bash
python get_images.py
```

That command has created the file: **flickr_tasks.json**. Now, let's use it to add the pictures to our project:

``` bash
pbs add_tasks --tasks-file flickr_tasks.json
```

Done! Again, a progress bar will show us how long it takes to add all
the tasks. Now that we have all the tasks in the project, we can work on the next step: presenting the tasks to the volunteers.

### Task's redundancy

PYBOSSA by default will send a task to different users (authenticated 
and anonymous users) until 30 different task runs are obtained for each task. This is usually known as **redundancy**, and we will use it to validate the analysis of the task. The whole aim of this value is to avoid trolls to participate several times in the same task, answering wrong on purpose so that we can have a valid statistical analysis of the submitted contributions by the volunteers.

PYBOSSA does not allow the same user to submit more than one
answer (task runs in PYBOSSA lingo) to the same task. PYBOSSA identifies anonymous users via their IP, while registered users via their PYBOSSA id.

Why PYBOSSA uses a default value of 30? Well, because we are getting 30 observations for a task, and if the data is normal, at least 30 samples should be obtained to get that model. In any case, you can easily change this value for each, using the task settings section of your project (or via the API using the [pbs](pbs.md#updating-tasks-redundancy-from-a-project) tool). 

If you want to improve the quality of the results for one task and get more confidence in the data when you will analyze it, you can modify the redundancy value with the pbs command. For example, to reduce the number of users that will analyze each task to ten, run the following command:

``` bash
pbs add_tasks --tasks-file file --redundancy 10
```

In this case, the **n_answers** field will instruct PYBOSSA to send the task to 10 different users.

### Tasks's priority

Every task can have its own **priority**. You can modify it using the web interface, or the API.

A task with a higher priority will be delivered first to the volunteers.
Hence if you have a project where you need to analyze a task first due to an external event (a new data sample has been obtained), then you can modify the priority of the newly created task and deliver it first.

If you have a new batch of tasks, instead of only a task, that needs to be processed before all the available ones, you can do it with pbs as well. Run the following command:

``` bash
pbs add_tasks --tasks-file file --priority 1
```

The priority is a number between 0.0 and 1.0. The highest priority is
1.0 and the lowest is 0.0.




## Presenting the Tasks to the user
Now that we have the tasks in our project, we have to present them to the user. For achieving this, you will have to create an HTML
template.

The template is the skeleton that will be used to load the data of the
tasks: the question, the photos, user progress, input fields & submit
buttons to solve the task.

In this tutorial, Flickr Person uses a basic HTML skeleton and the
[PYBOSSA.JS](https://github.com/Scifabric/pybossa.js) library to load the data of the tasks into the HTML template and take actions based on the users' answers.

!!! note
    When an authenticated user submits a task, the task will save the user ID. For anonymous users, the submitted task will only have the user IP address.

### The HTML Skeleton

The [file](https://github.com/Scifabric/app-flickrperson/blob/master/app-flickrperson/template.html)
**template.html** has the skeleton to show the tasks. The file has three sections:

- **A div for the warnings actions**. When the user saves an   answer, a success feedback message is shown to the user. There is
  also an error one for the failures.
- **A div for the Flickr image**. This div will be populated
  with the task photo URL and LINK data.
- **A div for the Questions & Answer buttons**. There are
  three buttons with the possible answers: *Yes*, *No*, and *I don't
  know*.

By default, PYBOSSA includes the PYBOSSA.JS
library, so you don't have to include it in your template.

All you have to do is to add a script section where you will be loading
the tasks and saving the answers from the users: <script></script>.

This template file will be used by the pbs command line tool to add the task presenter to the project. You can add it running the following command:

``` bash
pbs update_project
```

!!! note
    You can also edit the HTML skeleton using the web interface. Once the project has been created in PYBOSSA you will see a button that
    allows you to edit the skeleton using a WYSIWYG editor.

In PYBOSSA every project has a **task presenter** endpoint: http://PYBOSSA-SERVER/project/SLUG/newtask

!!! note
    The **slug** is the short name for the project, in this case
    **flickrperson**.

Loading the above endpoint will load the skeleton and trigger the
JavaScript functions to get a task from the PYBOSSA server and populate it in the HTML skeleton.

The header and footer for the presenter are already provided by PYBOSSA, so the template only has to define the structure to present the data from the tasks to the users and the action buttons, input methods, etc. to retrieve and save the answer from the volunteers.

#### Flickr Person Skeleton

For this tutorial, we have a very simple DOM. At the
beginning you will find a big div that will be used to show some
messages to the user about the success of an action, for instance, that an answer has been saved or that a new task is being loaded. Take a look:

``` html
<div class="row">
  <!-- Success and Error Messages for the user --> 
  <div class="span6 offset2" style="height:50px">
    <div id="success" class="alert alert-success" style="display:none;">
      <a class="close">×</a>
      <strong>Well done!</strong> Your answer has been saved
    </div>
    <div id="loading" class="alert alert-info" style="display:none;">
      <a class="close">×</a>
      Loading next task...
    </div>
    <div id="taskcompleted" class="alert alert-info" style="display:none;">
      <strong>The task has been completed!</strong> Thanks a lot!
    </div>
    <div id="finish" class="alert alert-success" style="display:none;">
      <strong>Congratulations!</strong> You have participated in all available tasks!
      <br/>
      <div class="alert-actions">
        <a class="btn small" href="/">Go back</a>
        <a class="btn small" href="/project">or, Check other projects</a>
      </div>
    </div>
    <div id="error" class="alert alert-error" style="display:none;">
      <a class="close">×</a>
      <strong>Error!</strong> Something went wrong, please contact the site administrators
    </div>
  </div> <!-- End Success and Error Messages for the user -->
</div> <!-- End of Row -->
```

Then we have the skeleton where we will be loading the Flickr photos, and the submission buttons for the user.

First, it creates a row that will have two columns (in Bootstrap a row
can have 12 columns), so we will populate a structure like this:

``` html
<div class="row skeleton">
    <!-- First column for showing the question, submission buttons and user
    progress -->
    <div class="span6"></div>
    <!-- Second column for showing the Flickr photo -->
    <div class="span6"></div>
</div>
```

The content for the first column where we will be showing the question of the task, the submission buttons with the answers: yes, no, and I don't know, and obviously the user progress for the user, so he can know how many tasks he has completed and how many are left. The code is the following:

``` html
<div class="span6 "><!-- Start of Question and Submission DIV (column) -->
    <h1 id="question">Question</h1> <!-- The question will be loaded here -->
    <div id="answer"> <!-- Start DIV for the submission buttons -->
        <!-- If the user clicks this button, the saved answer will be value="yes"-->
        <button class="btn btn-success btn-answer" value='Yes'><i class="icon icon-white icon-thumbs-up"></i> Yes</button>
        <!-- If the user clicks this button, the saved answer will be value="no"-->
        <button class="btn btn-danger btn-answer" value='No'><i class="icon icon-white icon-thumbs-down"></i> No</button>
        <!-- If the user clicks this button, the saved answer will be value="NotKnown"-->
        <button class="btn btn-answer" value='NotKnown'><i class="icon icon-white icon-question-sign"></i> I don't know</button>
    </div><!-- End of DIV for the submission buttons -->
    <!-- Feedback items for the user -->
    <p>You are working now on task: <span id="task-id" class="label label-warning">#</span></p>
    <p>You have completed: <span id="done" class="label label-info"></span> tasks from
    <!-- Progress bar for the user -->
    <span id="total" class="label label-inverse"></span></p>
    <div class="progress progress-striped">
        <div id="progress" rel="tooltip" title="#" class="bar" style="width: 0%;"></div>
    </div>
    <!-- 
        This project uses Disqus to allow users to provide some feedback.
        The next section includes a button that when a user clicks on it will
        load the comments, if any, for the given task
    -->
    <div id="disqus_show_btn" style="margin-top:5px;">
        <button class="btn btn-primary btn-large btn-disqus" onclick="loadDisqus()"><i class="icon-comments"></i> Show comments</button>
        <button class="btn btn-large btn-disqus" onclick="loadDisqus()" style="display:none"><i class="icon-comments"></i> Hide comments</button>
    </div><!-- End of Disqus Button section -->
    <!-- Disqus thread for the given task -->
    <div id="disqus_thread" style="margin-top:5px;display:none"></div>
</div><!-- End of Question and Submission DIV (column) -->
```

Then we will add the code for showing the photos. This second column will be much simpler:

``` html
<div class="span6"><!-- Start of Photo DIV (columnt) -->
    <a id="photo-link" href="#">
        <img id="photo" src="http://img339.imageshack.us/img339/9017/loadingo.png" style="max-width=100%">
    </a>
</div><!-- End of Photo DIV (column) -->
```

In the above code, we use a placeholder *loadingo.png* that we have
created previously, so we show an image while the first one from the
task is getting loaded.

The second section of the skeleton, if we join the previous snippets of code will be like this:

``` html
<div class="row skeleton"> <!-- Start Skeleton Row-->
    <div class="span6 "><!-- Start of Question and Submission DIV (column) -->
        <h1 id="question">Question</h1> <!-- The question will be loaded here -->
        <div id="answer"> <!-- Start DIV for the submission buttons -->
            <!-- If the user clicks this button, the saved answer will be value="yes"-->
            <button class="btn btn-success btn-answer" value='Yes'><i class="icon icon-white icon-thumbs-up"></i> Yes</button>
            <!-- If the user clicks this button, the saved answer will be value="no"-->
            <button class="btn btn-danger btn-answer" value='No'><i class="icon icon-white icon-thumbs-down"></i> No</button>
            <!-- If the user clicks this button, the saved answer will be value="NotKnown"-->
            <button class="btn btn-answer" value='NotKnown'><i class="icon icon-white icon-question-sign"></i> I don't know</button>
        </div><!-- End of DIV for the submission buttons -->
        <!-- Feedback items for the user -->
        <p>You are working now on task: <span id="task-id" class="label label-warning">#</span></p>
        <p>You have completed: <span id="done" class="label label-info"></span> tasks from
        <!-- Progress bar for the user -->
        <span id="total" class="label label-inverse"></span></p>
        <div class="progress progress-striped">
            <div id="progress" rel="tooltip" title="#" class="bar" style="width: 0%;"></div>
        </div>
        <!-- 
            This project uses Disqus to allow users to provide some feedback.
            The next section includes a button that when a user clicks on it will
            load the comments, if any, for the given task
        -->
        <div id="disqus_show_btn" style="margin-top:5px;">
            <button class="btn btn-primary btn-large btn-disqus" onclick="loadDisqus()"><i class="icon-comments"></i> Show comments</button>
            <button class="btn btn-large btn-disqus" onclick="loadDisqus()" style="display:none"><i class="icon-comments"></i> Hide comments</button>
        </div><!-- End of Disqus Button section -->
        <!-- Disqus thread for the given task -->
        <div id="disqus_thread" style="margin-top:5px;display:none"></div>
    </div><!-- End of Question and Submission DIV (column) -->
    <div class="span6"><!-- Start of Photo DIV (column) -->
        <a id="photo-link" href="#">
            <img id="photo" src="http://img339.imageshack.us/img339/9017/loadingo.png" style="max-width=100%">
        </a>
    </div><!-- End of Photo DIV (columnt) -->
</div><!-- End of Skeleton Row -->
```

### Loading the Task data

Now that we have set up the *skeleton* to load the task data, let's see
the JavaScript that we have to write to load the pictures from
Flickr and ask the volunteer an answer about them.

All the action takes place in the [file](https://github.com/Scifabric/app-flickrperson/blob/master/app-flickrperson/template.html) **template.html** script section.

The script is very simple; it uses the PYBOSSA.JS library to get a new task and to submit and save the answer in the server.

PYBOSSA.JS implements two methods that have to be overridden with some logic, as each project will have a different need, i.e., some projects will be loading another type of data in a different skeleton:

- pybossa.taskLoaded(function(task, deferred){});
- pybossa.presentTask(function(task, deferred){});

The **pybossa.taskLoaded** method will be in charge of adding new
**<img/>** objects to the DOM once they have been loaded from
Flickr (the URL is provided by the task object in the field
task.info.url_b), and resolve the deferred object, so another task for
the current user can be pre-loaded. The code is the following:

``` javascript
pybossa.taskLoaded(function(task, deferred) {
    if ( !$.isEmptyObject(task) ) {
        // load image from flickr
        var img = $('<img />');
        img.load(function() {
            // continue as soon as the image is loaded
            deferred.resolve(task);
        });
        img.attr('src', task.info.url_b).css('height', 460);
        img.addClass('img-polaroid');
        task.info.image = img;
    }
    else {
        deferred.resolve(task);
    }
});
```

The **pybossa.presentTask** method will be called when a task has been obtained from the server:

``` javascript
{ question: project.description,
  task: { 
          id: value,
          ...,
          info: { 
                  url_m: 
                  link:
                 } 
        } 
}
```

That JSON object will be accessible via the task object passed as an
argument to the pybossa.presentTask method. First, we will need to check that we are not getting an empty object, as it will mean that there are no more available tasks for the current user. In that case, we should hide the skeleton, and say thanks to the user as he has participated in all the tasks of the project.

If the task object is not empty, then we have a task to load into the
*skeleton*. In this demo project, we will update the
question, adding the photo to the DOM, refreshing the user progress and add some actions to the submission buttons so we can save the answer of the volunteer.

The PYBOSSA.JS library treats the user input as an "async function."
This is why the function gets a deferred object, as this object will be
*resolved* when the user clicks on one of the possible answers. We use this approach to load in the background the next task for the user while the volunteer is solving the current one. Once the answer has been saved in the server, we resolve the deferred:

``` javascript
pybossa.presentTask(function(task, deferred) {
    if ( !$.isEmptyObject(task) ) {
        loadUserProgress();
        $('#photo-link').html('').append(task.info.image);
        $("#photo-link").attr("href", task.info.link);
        $("#question").html(task.info.question);
        $('#task-id').html(task.id);
        $('.btn-answer').off('click').on('click', function(evt) {
            var answer = $(evt.target).attr("value");
            if (typeof answer != 'undefined') {
                //console.log(answer);
                pybossa.saveTask(task.id, answer).done(function() {
                    deferred.resolve();
                });
                $("#loading").fadeIn(500);
                if ($("#disqus_thread").is(":visible")) {
                    $('#disqus_thread').toggle();
                    $('.btn-disqus').toggle();
                }
            }
            else {
                $("#error").show();
            }
        });
        $("#loading").hide();
    }
    else {
        $(".skeleton").hide();
        $("#loading").hide();
        $("#finish").fadeIn(500);
    }
});
```

It is important to note that in this method we bind the *on-click*
action for the *Yes*, *No* and *I don't know* buttons to call the above snippet:

``` javascript
$('.btn-answer').off('click').on('click', function(evt) {
    var answer = $(evt.target).attr("value");
    if (typeof answer != 'undefined') {
        //console.log(answer);
        pybossa.saveTask(task.id, answer).done(function() {
            deferred.resolve();
        });
        $("#loading").fadeIn(500);
        if ($("#disqus_thread").is(":visible")) {
            $('#disqus_thread').toggle();
            $('.btn-disqus').toggle();
        }
    }
    else {
        $("#error").show();
    }
});
```

If your project uses other input methods, you will have to adapt this to fit your project needs.

Finally, the pybossa.presentTask calls a method named
**loadUserProgress**. This method is in charge of getting the user
the progress of the user and update the progress bar accordingly:

``` javascript
function loadUserProgress() {
    pybossa.userProgress('flickrperson').done(function(data){
        var pct = Math.round((data.done*100)/data.total);
        $("#progress").css("width", pct.toString() +"%");
        $("#progress").attr("title", pct.toString() + "% completed!");
        $("#progress").tooltip({'placement': 'left'}); 
        $("#total").text(data.total);
        $("#done").text(data.done);
    });
}
```

You can update the code only to show the number of answers, or remove it entirely. However, the volunteers will benefit from this type of information as they will be able to know how many tasks they have to do, giving an idea of progress while they contribute to the project.

Finally, we only need in our code to tell pybossa.js to run our project:

``` javascript
pybossa.run('flickrperson')
```

### Saving the answer

Once the task has been presented, users can click on the answer
buttons: **Yes**, **No** or **I don't know**.

*Yes* and *No* save the answer in the DB with information about the task and the answer, while the button *I don't know* loads another task as sometimes the image is not available.

To submit and save the answer from the user, we will use again
the PYBOSSA.JS library. In this case:

``` javascript
pybossa.saveTask( taskid, answer )
```

The *pybossa.saveTask* method saves an answer for a given task. In the previous section, we show that in the pybossa.presentTask method the *task-id* can be obtained, as we will be passing the object to saveTask method.

The method allows us to give a successful pop-up feedback for the user, so you can use the following structure to warn the user and tell him that his answer has been successfully saved:

``` javascript
pybossa.saveTask( taskid, answer ).done(
  function( data ) {
      // Show the feedback div
      $("#success").fadeIn(); 
      // Fade out the pop-up after a 1000 miliseconds
      setTimeout(function() { $("#success").fadeOut() }, 1000);
  };
);
```

### Keeping track of the time spent by volunteers solving a task
Since v1.1.3, PYBOSSA records a timestamp, for every task run, of the contributed task runs. This is stored in the "created" attribute of the Task Runs.

Now, with the "finish_time" attribute, we will be able to know how
much time the volunteer has spent completing the task: (time spent =
finish_time - created)

!!! tip
     This information is only shown to the owner of the project.

###  Updating the template for all the tasks

It is possible to update the template of the project without having to re-create the project and its tasks. To update the template,
you only have to modify the file *template.html* and run the following command:

``` bash
pbs update_project
```

You can also use the web interface to do it, and see the changes in real time before saving the results. Check your project page, go to the tasks section, and look for the **Edit the task presenter** button.

### Testing the task presenter

To test the project task presenter, go to the following URL     http://PYBOSSA-SERVER/project/SLUG/presenter

The presenter will load one task, and you will be able to submit and
save one answer for the current task.

## Publishing the project
Until now, the project has been in testing mode. This means that you can play with your project as much as you want. You can invite a few friends or colleauges to test it, just to know that everything works. 

In this mode PYBOSSA will work as a published project, so you can test it, however, it is not published so it's not visible to users on the server unless you share the link to it. 

Once you are happy, you can publish the project. When you publish the project, PYBOSSA will clean your tests and leave it clean for your contributors. Thus, don't be afraid and test as much as you want!

## Tutorial

In general, users will like to have some feedback when accessing for the very first time your project. Usually, the overview page of your project will not be enough, so you can build a tutorial (a web page) that will explain to the volunteer how he can participate in the
project.

PYBOSSA will detect if the user is accessing for the very first time
your project, so in that case, it will load the **tutorial**, if your project has one.

Adding a tutorial is simple: you only have to create a file named
**tutorial.html** and load the content of the file using pbs:

``` bash
pbs update_project
```

The tutorial could have whatever you like: videos, nice animations, etc. PYBOSSA will render for you the header and the footer, so you only have to focus on the content. You can copy the template.html file and use it as a draft of your tutorial or just include a video of yourself explaining why your project is important and how, as a volunteer, you can contribute.

If your project has a tutorial, you can access it directly in
this endpoint:

    http://server/project/tutorial

### Advance tutorial (helping materials)

While the previous solution works for most of the projects, your project might need something special: visual clues so users can easily identify sounds, patterns, etc. easily. The default tutorial does not allow you to curate/create a list of helping materials that could be used directly in the presenter to explain how for example you can identify cancer cells, or specific species of animals.

For this reason, PYBOSSA now supports an API endpoint for helping
materials: api/helpingmaterial.

This endpoint allows you to add JSON and media files (images, videos or sounds) that you can use within your project to build an interactive tutorial.

Helping materials allow you to upload images via the endpoint using the multipart/form-data Content-Type.

For example, imagine that you want to add a photo of an animal and it's description, so users can easily identify it (or use it as pre-loaded
answer for classifying pictures of animals). In this case, you can do
the following (using the popular Python requests library, but you can
use any other programming language):

``` python
import requests
url = 'https://server/api/helpingpoint?api_key=YOURKEY'
# Upload a picture
files = {'file': open('test.jpg', 'rb')}
data = {'project_id': YOURPROJECT_ID}
r = requests.post(url, data=data, files=files)
# Get the created helping material
hp = r.json()
# Add the meta-data of the picture
url = 'https://server/api/helpingpoint/%s?api_key=YOURKEY' % hp['id']
info = {'popular_name': 'elephant', 'scientific_name': 'loxodonta'}
r = requests.put(url, json={'info': info})
```

You can add as many files as you want. Then, from any place you can
query the helping material endpoint to retrieve the example/tutorial
materials for helping your users.

!!! tip PBS and helping materials
     You can use PBS to add helping materials from an Excel or CSV file. Check the [documentation](pbs.md#helping-materials).

## Providing some I18n support

Sometimes, you may want to provide the task interface in their language. To support this, you can access their locale via Javascript in an effortless way, as we've placed the user locale in a hidden 'div' node:

``` javascript
var userLocale = document.getElementById('PYBOSSA_USER_LOCALE').textContent.trim();
```

The way you use it after that is up to you. But let's see an example of
how you can use it to make a tutorial that automatically shows the
strings in the locale of the user.

!!! warning
    Anonymous users will be only shown with **en** language by default. This feature only works for authenticated users that choose their locale in their account. You can, however, load the translated strings using the browser preferred language.


First of all, check the *tutorial.html file*. You will see it consists on some HTML plus some Javascript inside a script tag to handle the different steps of the tutorial. Here you have a snippet of HTML tutorial file:

``` html
<div class="row">
    <div class="col-md-12">
        <div id="modal" class="modal hide fade">
            <div class="modal-header">
                <h3>Flickr Person Finder tutorial</h3>
            </div>
            <div id="0" class="modal-body" style="display:none">
                <p><strong>Hi!</strong> This is a <strong>demo project</strong> that shows how you can do pattern recognition on pictures or images using the PYBOSSA framework.
               </p>
            </div>
            <div id="1" class="modal-body" style="display:none">
                <p>The project is really simple. It loads a photo from <a href="http://flickr.com">Flickr</a> and asks you this question: <strong>Do you see a human in this photo?</strong></p>
                <img src="http://farm7.staticflickr.com/6109/6286728068_2f3c6912b8_q.jpg" class="img-thumbnail"/>
                <p>You will have 3 possible answers:
                <ul>
                    <li>Yes,</li>
                    <li>No, and</li>
                    <li>I don't know</li>
                </ul>
                </p>
                <p>
                </p>
                <p>All you have to do is to click in one of the three possible answers and you will be done. This demo project could be adapted for more complex pattern recognition problems.</p>
            </div>
            <div class="modal-footer">
                <a id="prevBtn" href="#" onclick="showStep('prev')" class="btn">Previous</a>
                <a id="nextBtn" href="#" onclick="showStep('next')" class="btn btn-success">Next</a>
                <a id="startContrib" href="../flickrperson/newtask" class="btn btn-primary" style="display:none"><i class="fa fa-thumbs-o-up"></i> Try the demo!</a>
            </div>
        </div>
    </div>
</div>
```

To add multilingual support, copy and paste it is as many times as
languages you're planning to support.

Then, add to each of them an id in the outermost 'div' which
corresponds to the name of the locale ('en' for English, 'es'
for Spanish, etc.), and translate the inner text of it, but leave all
the HTML the same in every version (tags, ids, classes, etc.) like:

``` html
<div id='es' class="row">
   Your translated version of the HTML goes here, but only change the text,
   NOT the HTML tags, IDs or classes.
</div>
```

Finally, in the Javascript section of the tutorial, you will need to add some extra code to enable multilingual tutorials. Thus, modify the
javascript from:

``` javascript
var step = -1;
function showStep(action) {
    $("#" + step).hide();
    if (action == 'next') {
        step = step + 1;
    }
    if (action == 'prev') {
        step = step - 1;
    }
    if (step == 0) {
        $("#prevBtn").hide();
    }
    else {
        $("#prevBtn").show();
    }

    if (step == 1 ) {
        $("#nextBtn").hide();
        $("#startContrib").show();
    }
    $("#" + step).show();
}

showStep('next');
$("#modal").modal('show');
```

To:

``` javascript
var languages = ['en', 'es']
$(document).ready(function(){
    var userLocale = document.getElementById('PYBOSSA_USER_LOCALE').textContent.trim();
    languages.forEach(function(lan){
        if (lan !== userLocale) {
            var node = document.getElementById(lan);
            if (node.parentNode) {
                node.parentNode.removeChild(node);
            }
        }
    });
    var step = -1;
    function showStep(action) {
        $("#" + step).hide();
        if (action == 'next') {
            step = step + 1;
        }
        if (action == 'prev') {
            step = step - 1;
        }
        if (step == 0) {
            $("#prevBtn").hide();
        }
        else {
            $("#prevBtn").show();
        }

        if (step == 1 ) {
            $("#nextBtn").hide();
            $("#startContrib").show();
        }
        $("#" + step).show();
    }
    showStep('next');
    $("#modal").modal('show');
});
```

Notice the languages array variable defined at the beginning?. It's
vital that you place there the IDs you've given to the different translated versions of your HTML for the tutorial. The rest of the
script will only compare the locale of the user that is seeing the
tutorial and delete all the HTML that is not in his language, so that
only the tutorial that fits his locale settings is shown.

### Another method to support I18n

Another option for translating your project to different languages is
using a JSON object like this:

``` javascript
messages = {"en": 
               {"welcome": "Hello World!,
                "bye": "Good bye!"
               },
            "es:
               {"welcome": "Hola mundo!",
                "bye": "Hasta luego!"
               }
           }
```

This object can be placed in the *tutorial.html* or *template.html* file to load the proper strings translated to your users.

The logic is very simple. With the following code you grab the language that should be loaded for the current user:

``` javascript
var userLocale = document.getElementById('PYBOSSA_USER_LOCALE').textContent.trim();
```

Now, use userLocale to load the strings. For example, for
*template.html* and the Flickrperson demo project, you will find the
following code at the start of the script:

``` javascript
// Default language
var userLocale = "en";
// Translations
var messages = {"en": {
                        "i18n_welldone": "Well done!",
                        "i18n_welldone_text": "Your answer has been saved",
                        "i18n_loading_next_task": "Loading next task...",
                        "i18n_task_completed": "The task has been completed!",
                        "i18n_thanks": "Thanks a lot!",
                        "i18n_congratulations": "Congratulations",
                        "i18n_congratulations_text": "You have participated in all available tasks!",
                        "i18n_yes": "Yes",
                        "i18n_no_photo": "No photo",
                        "i18n_i_dont_know": "I don't know",
                        "i18n_working_task": "You are working now on task:",
                        "i18n_tasks_completed": "You have completed:",
                        "i18n_tasks_from": "tasks from",
                        "i18n_show_comments": "Show comments:",
                        "i18n_hide_comments": "Hide comments:",
                        "i18n_question": "Do you see a human face in this photo?",
                      },
                "es": {
                        "i18n_welldone": "Bien hecho!",
                        "i18n_welldone_text": "Tu respuesta ha sido guardada",
                        "i18n_loading_next_task": "Cargando la siguiente tarea...",
                        "i18n_task_completed": "La tarea ha sido completadas!",
                        "i18n_thanks": "Muchísimas gracias!",
                        "i18n_congratulations": "Enhorabuena",
                        "i18n_congratulations_text": "Has participado en todas las tareas disponibles!",
                        "i18n_yes": "Sí",
                        "i18n_no_photo": "No hay foto",
                        "i18n_i_dont_know": "No lo sé",
                        "i18n_working_task": "Estás trabajando en la tarea:",
                        "i18n_tasks_completed": "Has completado:",
                        "i18n_tasks_from": "tareas de",
                        "i18n_show_comments": "Mostrar comentarios",
                        "i18n_hide_comments": "Ocultar comentarios",
                        "i18n_question": "¿Ves una cara humana en esta foto?",
                      },
               };
// Update userLocale with server side information
 $(document).ready(function(){
     userLocale = document.getElementById('PYBOSSA_USER_LOCALE').textContent.trim();

});

function i18n_translate() {
    var ids = Object.keys(messages[userLocale])
    for (i=0; i<ids.length; i++) {
        console.log("Translating: " + ids[i]);
        document.getElementById(ids[i]).innerHTML = messages[userLocale][ids[i]];
    }
}
```

First, we define the default locale, "en" for English. Then, we create a
messages dictionary with all the ids that we want to translate. Finally, we add the languages that we want to support.

As you can see, it's quite simple as you can share the messages object with your volunteers so that you can get many more translations for your project smoothly.

Finally, we need actually to load those translated strings into the
template. For doing this step, all we've to do is adding the following
code to our *template.html* file at the function pybossa.presentTask:

``` javascript
pybossa.presentTask(function(task, deferred) {
    if ( !$.isEmptyObject(task) ) {
        loadUserProgress();
        i18n_translate();
        ...
```

Done! When the task is loaded, the strings are translated and the
project will be shown in the user language.


## Updating project's status
You can share the progress of the project creating a blog. Every PYBOSSA project includes a blog where you will be able to write about your project regularly.

You can use Markdown or plain text for the content of the posts. And you will also be able to edit them or delete after creation if you want.

To write a post go to the project **Settings** tab and there you
will find an option to write, read or delete your blog posts.

You can use the endpoint /api/blogpost to also add blogposts, update
them and delete them. The api endpoint allows you as well to upload a picture to your blogpost.

This endpoint allows you to add JSON and media files (images, videos or sounds) that you can use with your blogpost.

You can use this endpoint for uploading images via the endpoint using the multipart/form-data Content-Type.

For example, imagine that you want to add a photo as a cover and then the body of the blogpost. In this case, you can do the following (using the popular Python requests library, but you can use any other
programming language):

``` python
import requests
url = 'https://server/api/blogpost?api_key=YOURKEY'
# Upload a picture
files = {'file': open('test.jpg', 'rb')}
data = {'project_id': YOURPROJECT_ID, title='title', body='body'}
r = requests.post(url, data=data, files=files)
# Get the created blogpost
bp = r.json()
# Update the body with the meta-data of the picture
url = 'https://server/api/blogpost/%s?api_key=YOURKEY' % bp['id']
body = 'hello ![img](%s)' % bp['media_url']
r = requests.put(url, json={'body': body})
```

## Exporting the project's data
You can export all the available data  your project in three different ways:

-   [JSON](http://en.wikipedia.org/wiki/JSON), an open standard designed for human-readable data interchange, or
-   [CSV](http://en.wikipedia.org/wiki/Comma-separated_values), a file
    that stores tabular data (numbers and text) in plain-text form and
    that can be opened with almost any spreadsheet software, or
-   [CKAN](http://ckan.org) web server, a powerful data management
    a system that makes data accessible –by providing tools to streamline publishing, sharing, finding and using data.

For exporting the data, all you have to do is to visit the following URL
in your web-browser:

    http://PYBOSSA-SERVER/project/slug/tasks/export

You will find an interface that will allow you to export the Tasks, Task Runs and Results to [JSON](http://en.wikipedia.org/wiki/JSON) and [CSV](http://en.wikipedia.org/wiki/Comma-separated_values) formats:

![image](https://i.imgur.com/m5gDyjU.png)

The previous methods will export all the tasks, results and task runs, **even if they are not completed**. When a task has been completed, in other words, when a task has collected the number of answers specified by the task (**n_answers** = 30 by default), a **brown button** with the text **Download results** will pop up, and if you click it all the answers for the given task will be shown in JSON format.

You can check which tasks are completed, in the following URL:

    http://PYBOSSA-SERVER/project/slug

And clicking on the **Tasks** link in the **left local navigation**, and
then click in the **Browse** box:

![image](https://i.imgur.com/nauht7l.png)

Then you will see which tasks are completed, and which ones you can download in [JSON](http://en.wikipedia.org/wiki/JSON) format:

![image](https://i.imgur.com/pf5O5Tr.png)

You could download the results also using the API. For example, you
could write a small script that gets the list of tasks that have been
completed using this URL:

    GET http://PYBOSSA-SERVER/api/task?state=completed

!!! note
    If your project has more than 20 tasks, then you will need to use the [API pagination](api.md#list), as by default PYBOSSA API only returns the first 20 items.

Once you have obtained the list of completed tasks, your script could
start requesting the collected answers for the given tasks:

    GET http://PYBOSSA-SERVER/api/taskrun?task_id=TASK-ID

That way you will be able to get all the submitted answers by the
volunteers for the given task.


### Exporting the task, task runs and results in JSON

For the [JSON](http://en.wikipedia.org/wiki/JSON) format, you will get
all the output as a file that your browser will download, named:
short_name_tasks.json for the tasks, and short_name_task_runs.json
for the task runs.

### Exporting the task, task runs and results to a CSV file

While for the [CSV](http://en.wikipedia.org/wiki/Comma-separated_values) format, you will get a CSV file that will be automatically saved on your computer.

### Exporting the task, task runs and results to a CKAN server

If the server has been configured to allow you to export your
project's data to a CKAN server, the owner of the project will see another box that will give you the option to export the data to the CKAN server.

To use this method, you will need to add the CKAN API-KEY
associated with your account, otherwise, you will not be able to export the data, and a warning message will let you know it.

Adding the CKAN API-KEY is simple. You only need to create an
account in the supported CKAN server, check your profile and copy the API-KEY. Then, open your PYBOSSA account page, edit it and paste the key in the section **External Services**.

![image](https://i.imgur.com/xOezl6C.png)

Then, you will be able to export the data to the CKAN server
and host it there.

## Publishing results of your project

Since v1.2.0, PYBOSSA automatically creates "empty" results when a task is completed.

For example, imagine your project is asking the following question in a set of images: "Do you see a triangle in this picture?" The possible
answers are: yes and no.

Your project has configured the task redundancy to 5, so five people will answer that question for a given image (or task). When the 5th person sends the answer, the server marks the task as completed, and it creates a result for the given task associating the answers, the task and the project:

``` javascript
{"id": 1,
 "project_id": 1,
 "task_id": 1,
 "task_run_ids": [1,2,3,4,5],
 "info": null}
```

As in other PYBOSSA domain objects, a result has a JSON field named
**info** that allows you to store the **final result** for that task
using the task_runs 1, 2, 3, 4, 5. Imagine that the five volunteers
answered: yes, then as you are the project owner you could update the info field with that value:

``` javascript
{"id": 1,
 "project_id": 1,
 "task_id": 1,
 "task_run_ids": [1,2,3,4,5],
 "info": {"triangle": "yes"}}
```

The benefit of storing that information is that you can access these
data via the PYBOSSA API so you will be able to show the results, in
your result project section using the API.

This will allow you to build beautiful visualizations of your results on
maps, WebGL, etc.

## API Errors
If something goes wrong, you should get an error message similar to the following one:

``` javascript
    ERROR:root:pbclient.create_project
    {
        "action": "POST",
        "exception_cls": "IntegrityError",
        "exception_msg": "(IntegrityError) duplicate key value violates unique constraint \"project_name_key\"\nDETAIL:  Key (name)=(Flickr Person Finder) already exists.\n",
        "status": "failed",
        "status_code": 415,
        "target": "project"
    }
```

The error message will have the information regarding the problems it has found when using the API.

!!! note
    Since version 2.0.1 PYBOSSA enforces API Rate Limiting, so you might exceed the number of allowed requests, getting a 429 error. Please see [rate-limiting section](api.md#rate-limiting).
