Gitea
======

You can import tasks from your Gitea instance using
the ``gitea`` service name.

Example Service
---------------

Here's an example of a Gitea target:

.. config::

	[user_gitea]
	service = gitea
	gitea.username = ralphbean
	gitea.host = git.bean.com #Note: the lack of https, the service will assume HTTPS by default.
	gitea.token = @oracle:eval:pass show 'git.bean.com token'

The above example is the minimum required to import issues from
Gitea.  You can also feel free to use any of the
configuration options described in :ref:`common_configuration_options`
or described in `Service Features`_ below.

The ``token`` is your private API token.

Service Features
----------------

Include and Exclude Certain Repositories
++++++++++++++++++++++++++++++++++++++++

If you happen to be working with a large number of projects, you
may want to pull issues from only a subset of your repositories.  To
do that, you can use the ``include_repos`` option.

For example, if you would like to only pull-in issues from
your own ``project_foo`` and team ``bar``'s ``project_fox`` repositories, you
could add this line to your service configuration (replacing ``me`` by your own
login):

.. config::
    :fragment: gitea

    gitea.include_repos = me/project_foo, bar/project_fox

Alternatively, if you have a particularly noisy repository, you can
instead choose to import all issues excepting it using the
``exclude_repos`` configuration option.

In this example, ``noisy/repository`` is the repository you would
*not* like issues created for:

.. config::
    :fragment: gitea

    gitea.exclude_repos = noisy/repository

.. hint::
   If you omit the repository's namespace, bugwarrior will automatically add
   your login as namespace. E.g. the following are equivalent:

.. config::
    :fragment: gitea

    gitea.login = foo
    gitea.include_repos = bar

and:

.. config::
    :fragment: gitea

    gitea.login = foo
    gitea.include_repos = foo/bar

Alternatively, you can use project IDs instead of names by prefixing the
project id with `id:`:

.. config::
    :fragment: gitea

    gitea.include_repos = id:1234,id:3141

Import Labels as Tags
+++++++++++++++++++++

The gitea issue tracker allows you to attach labels to issues; to
use those labels as tags, you can use the ``import_labels_as_tags``
option:

.. config::
    :fragment: gitea

    gitea.import_labels_as_tags = True

Also, if you would like to control how these labels are created, you can
specify a template used for converting the gitea label into a Taskwarrior
tag.

For example, to prefix all incoming labels with the string 'gitea_' (perhaps
to differentiate them from any existing tags you might have), you could
add the following configuration option:

.. config::
    :fragment: gitea

    gitea.label_template = gitea_{{label}}

In addition to the context variable ``{{label}}``, you also have access
to all fields on the Taskwarrior task if needed:

.. note::

   See :ref:`field_templates` for more details regarding how templates
   are processed.

Limit Issues Imported
+++++++++++++++++++++
Gitea lets system administrators configure the amount of objects that any given API request will return. 
You may configure the amount to tell Gitea to give to you using the ``issue_limit`` option:

.. config::
   :fragment: gitea
   
    gitea.issue_limit = 200

Do note, this will not overwrite what the gitea instance limits you to, it merely lets you set the amount of issues you will import.


Including various types of issues
+++++++++++++++++++++

Gitea has metadata attached to each issue, primarily: If you are assigned to an issue, if you created an issue, if an issue mentions you, and if an issue has a review reqest for you. You may set if each of these traits is worth importing by using the various ``include_*_issues`` options:

.. config::
   :fragment: gitea
    
    gitea.include_assigned_issues = true
    gitea.include_created_issues = true
    gitea.include_mentioned_issues = true
    gitea.include_review_requested_issues = true

Each setting will query the API for that trait alone and then add it to your Taskwarrior task list. For example, if you have created issues and mentioned issues off, but assigned issues and review requested issues on: You will only recieve new tasks for the issues you are assigned to do or requested to review, but not for issues you've created or mentioned. Issues that have been assigned to you and created by you would be included though, as these settings merely mark inclusion, not exclusion.

Provided UDA Fields
-------------------

.. udas:: bugwarrior.services.gitea.GiteaIssue
