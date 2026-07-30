.. meta::
   :description: Guide to proposing a Launchpad database patch for deployment.

.. _propose-a-db-patch:

Propose a database patch for deployment
=======================================

Before deploying a database patch, you need to make sure it applies cleanly
and things keep working. It's also needed to pass the performance requirements.

QA a database patch
~~~~~~~~~~~~~~~~~~~

To QA the patch, you will need to follow different steps if the patch is a cold
or a hot one.

Cold patches
^^^^^^^^^^^^

* Wait until the branch reaches staging. Use the `db-stable deployment
  report <https://deployable.ols.canonical.com/project/launchpad-db>`__
  to check this.
* After the branch reaches staging check the duration that the patch took to
  be applied by reading the logs in ``launchpad-bastion-ps5`` as the
  ``stg-launchpad`` user: ``less ~/logs/<date>-staging_restore.log``
  If it took more than 15 seconds, mark the revision as non deployable and
  revert it.
* Check that things still work.

Hot patches
^^^^^^^^^^^

* Operations that claim locks on tables should be done using ``CONCURRENTLY``.
* Manually apply the patch to qastaging: ssh into the db with write access
  and apply the patch. Make sure to include the patch revision row to
  ``LaunchpadDatabaseRevision`` so it does not get applied twice.
* Check that things still work.

Ask for a deployment
~~~~~~~~~~~~~~~~~~~~

In case it passed the QA, commit it without sample data changes, push and
propose for merging to ``db-devel`` if it's a cold patch or ``master`` if
it's a hot patch.

The schema change is approved for landing when you have an 'Approved'
vote from a DB reviewer (unless the reviewer in question explicitly
sets a higher barrier).

In case you're not member of ``~launchpad``, ask a member of the team to do
these last steps for you:

* Wait until there are **no** blockers to deploying the patch. One
  common blocker is having code changes made prior to the patch sitting
  in stable and not yet deployed to all affected service instances.
* Request a deployment per the internal `production change
  process <https://canonical-launchpad-admin-manual.readthedocs-hosted.com/en/latest/howto/launchpad-rollout/launchpad-deployments/production-change-processes/>`__.

