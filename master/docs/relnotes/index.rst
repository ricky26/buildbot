Release Notes for Buildbot ``|version|``
========================================

..
    Any change that adds a feature or fixes a bug should have an entry here.
    Most simply need an additional bulleted list item, but more significant
    changes can be given a subsection of their own.

    If you can:

       please point to the bug using syntax: (:bug:`NNN`)
       please point to classes using syntax: :py:class:`~buildbot.reporters.http.HttpStatusBase`

..
    NOTE: When releasing 0.9.0, combine these notes with those from 0.9.0b*
    into one single set of notes.  Also, link prominently to the migration guide.

The following are the release notes for Buildbot ``|version|``.

See :ref:`Upgrading to Nine` for a guide to upgrading from 0.8.x to 0.9.x

Master
------

Features
~~~~~~~~

* Add support for hyper.sh via :class:`HyperLatentWorker`
  Hyper_ is a CaaS solution for hosting docker container in the cloud, billed to the second.
  It forms a very cost efficient solution to run your CI in the cloud.

* The :bb:step:`Trigger` step now supports ``unimportantSchedulerNames``

* add a UI button to allow to cancel the whole queue for a builder

* Buildbot log viewer now support 256 colors ANSI codes

* new :bb:step:`GitHub` which correctly checkout the magic branch like ``refs/pull/xx/merge``.

* :class:`MailNotifier` now supports a `schedulers` constructor argument that
  allows you to send mail only for builds triggered by the specified list of
  schedulers.

* :class:`MailNotifier` now supports a `branches` constructor argument that
  allows you to send mail only for builds triggered by the specified list of
  branches.

* Optimization of the data api filtering, sorting and paging, speeding up a lot the UI when the master has lots of builds.

* :bb:reporter:`GerritStatusPush` now accepts a ``notify`` parameter to control who gets emailed by Gerrit.

* Add a ``format_fn`` parameter to the ``HttpStatusPush`` reporter to customize the information being pushed.

* Latent Workers can now start in parallel.

    * The build started by latent worker will be created while the latent worker is substantiated.

    * Latent Workers will now report startup issues in the UI.

* Workers will be temporarily put in quarantine in case of build preparation issues.
    This avoids master and database overload in case of bad worker configuration.
    The quarantine is implemented with an exponential back-off timer.

* Master Stop will now stop all builds, and wait for all workers to properly disconnect.
  Previously, the worker connections was stopped, which incidentally made all their builds marked retried.
  Now, builds started with a :class:`Triggereable` scheduler will be cancelled, while other builds will be retried.
  The master will make sure that all latent workers are stopped.

* The ``MessageFormatter`` class also allows inline-templates with the ``template`` parameter.

* The ``MessageFormatter`` class allows custom mail's subjects with the ``subject`` and ``subject_name`` parameters.

* The ``MessageFormatter`` class allows extending the context given to the Templates via the ``ctx`` parameter.

* The new ``MessageFormatterMissingWorker`` class allows to customize the message sent when a worker is missing.

.. _Hyper: https://hyper.sh

Fixes
~~~~~

* fix the UI to allow to cancel a buildrequest (:bug:`3582`)

* :bb:chsrc:`GitHub` change hook now correctly use the refs/pull/xx/merge branch for testing PRs.

* Fix the UI to better adapt to different screen width (:bug:`3614`)

* Don't log :class:`AlreadyClaimedError`.
  They are normal in case of :bb:step:`Trigger` cancelling, and in a multimaster configuration.

* Fix issues with worker disconnection.
  When a worker disconnects, its current buildstep must be interrupted and the buildrequests should be retried.

* Fix the worker missing email notification.

Changes for Developers
~~~~~~~~~~~~~~~~~~~~~~

Features
~~~~~~~~

* New :class:`SharedService` can be used by steps, reporters, etc to implement per master resource limit.


Fixes
~~~~~


Deprecations, Removals, and Non-Compatible Changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* By default, non-distinct commits received via
  :class:`buildbot.status.web.hooks.github.GitHubEventHandler` now get recorded
  as a :class:`Change`. In this way, a commit pushed to a branch that is not
  being watched (e.g. a dev branch) will still get acted on when it is later
  pushed to a branch that is being watched (e.g. master). In the past, such a
  commit would get ignored and not built because it was non-distinct. To disable
  this behavior and revert to the old behavior, install a :class:`ChangeFilter`
  that checks the ``github_distinct`` property:

.. code-block:: python

  ChangeFilter(filter_fn=lambda c: c.properties.getProperty('github_distinct'))


* setup.py 'scripts' have been converted to console_scripts entry point.
  This makes them more portable and compatible with wheel format.
  Most consequences are for the windows users:

  * ``buildbot.bat`` does not exist anymore, and is replaced by ``buildbot.exe``, which is generated by the console_script entrypoint.

  * ``buildbot_service.py`` is replaced by ``buildbot_windows_service.exe``, which is generated by the console_script entrypoint
    As this script has been written in 2006, has only inline documentation and no unit tests, it is not guaranteed to be working.
    Please help improving the windows situation.

* The ``user`` and ``password`` parameters of the ``HttpStatusPush`` reporter have been deprecated in favor of the ``auth`` parameter.

* The ``template_name`` parameter of the ``MessageFormatter`` class has been deprecated in favor of ``template_filename``.


Buildslave
----------

Deprecations, Removals, and Non-Compatible Changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~


Worker
------

Fixes
~~~~~

Changes for Developers
~~~~~~~~~~~~~~~~~~~~~~

Deprecations, Removals, and Non-Compatible Changes
~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~

* setup.py 'scripts' have been converted to console_scripts entry point.
  This makes them more portable and compatible with wheel format.
  Most consequences are for the windows users:

  * ``buildbot_worker.bat`` does not exist anymore, and is replaced by ``buildbot_worker.exe``, which is generated by the console_script entrypoint.

  * ``buildbot_service.py`` is replaced by ``buildbot_worker_windows_service.exe``, which is generated by the console_script entrypoint
    As this script has been written in 2006, has only inline documentation and no unit tests, it is not guaranteed to be working.
    Please help improving the windows situation.

* :class:`AbstractLatentWorker` is now in :py:mod:`buildbot.worker.latent` instead of :py:mod:`buildbot.worker.base`.

Details
-------

For a more detailed description of the changes made in this version, see the git log itself:

.. code-block:: bash

   git log v0.9.0rc1..master

Older Versions
--------------

Release notes for older versions of Buildbot are available in the :src:`master/docs/relnotes/` directory of the source tree.
Newer versions are also available here:

.. toctree::
    :maxdepth: 1

    0.9.0
    0.9.0rc4
    0.9.0rc3
    0.9.0rc2
    0.9.0rc1
    0.9.0b9
    0.9.0b8
    0.9.0b7
    0.9.0b6
    0.9.0b5
    0.9.0b4
    0.9.0b3
    0.9.0b2
    0.9.0b1
    0.8.12
    0.8.10
    0.8.9
    0.8.8
    0.8.7
    0.8.6

Note that Buildbot-0.8.11 was never released.
