B2G new FxOS Version
====================
.. seealso:: :ref:`Disclaimer <workflow_disclaimer>`

.. :caption: New b2g versions
.. actdiag::
    :desctable:
    :alt: Inter-team handoffs for new b2g versions
    :name: Figure1

    {
        coord_timing ->
        file_bug_tree ->
        create_repos;
        create_repos -> setup_automation_configs 
                     -> modify_treeherder
                     -> ready_for_golive;
        create_repos -> setup_vcs_sync 
                     -> ready_for_golive;
        create_repos -> branch_git_repos
                     -> ready_for_golive;
        ready_for_golive ->
        go_live ->
        debug_setup ->
        done;

        lane b2g_pm {
            label = "FxOS PM"
            coord_timing
            file_bug_tree
            ready_for_golive
            go_live
            done
            }

        lane b2g_devs {
            label = "FxOS Devs"
            branch_git_repos
            debug_setup
            }

        lane releng {
            label = "Release Engineering"
            setup_automation_configs
            modify_treeherder
            }

        lane dev_srvcs {
            label = "VCS Team"
            create_repos
            setup_vcs_sync
            }
            
    }
