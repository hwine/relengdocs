Git commit to Build start
=========================

In general at Mozilla, commits to version control systems are made via
ssh, while checkouts of code are made via https to a web server. With larger
systems, a common scaling technique is to provide multiple web servers
(aka "web heads") behind a load balancer and distinct from the commit node.

This introduces a possible consistency issue, as it does take some time
to propagate a changset from the commit node to all of the web heads.
While individuals are unlikely to notice this latency, automation
sometimes does. For hg.mozilla.org, various approaches have been taken
to provide a minimal window that rarely causes issues for automation
under normal conditions.

As we consider adding multiple web heads to git.mozilla.org, it is
important to understand the latency needs of the automation system.


.. :caption: Github commit to Build start
.. actdiag::
    :desctable:
    :alt: Github Gaia commit to Build start
    :name: Figure1

    {
        github_commit -> mirror_internally;
        mirror_internally -> git_ssh_node;
        mirror_internally -> hg_ssh_node;
        hg_ssh_node -> include_in_gecko;
        include_in_gecko -> hg_ssh_node;
        hg_ssh_node -> schedule_builds;
        schedule_builds -> start_builds [color="yellow", style="dashed"];
        hg_web_heads -> start_builds;
        git_web_heads -> start_builds [color="green"];

        // replication
        git_ssh_node -> git_web_heads;
        hg_ssh_node -> hg_web_heads;

        lane developer {
            label = "Dev";
            github_commit [label = "Push to Github"];
        }

        lane vcs_land {
            label = "VCS Sync";
            mirror_internally [label = "vcs-sync"]
            git_ssh_node [label = "git r/w"]
            git_web_heads [label = "git r/o"]
            hg_ssh_node [label = "hg r/w"]
            hg_web_heads [label = "hg r/o"]
        }

        lane ci_land {
            label = "CI Tooling"
            include_in_gecko [label = "bumper"];
            schedule_builds [label = "scheduler"];
            start_builds [label = "build hosts"];
        }
    }
            

.. |br| raw:: html

    <br />
