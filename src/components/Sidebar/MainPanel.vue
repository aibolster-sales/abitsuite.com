<template>
    <aside class="aside aside-fixed">
        <div class="aside-header">
            <a href="javascript://" class="aside-logo">
                <img src="@/assets/img/banner-icon-32.png" alt="aBitSuite Logo">
                <span>a</span>Bit<span>Suite</span>
            </a>

            <a href="javascript://" class="aside-menu-link">
                <i data-feather="menu"></i>
                <i data-feather="x"></i>
            </a>
        </div>

        <div class="aside-body">
            <div class="aside-loggedin">
                <div class="d-flex align-items-center justify-content-start">
                    <a href="javascript://" class="avatar"><img src="@/assets/img/img1.png" class="rounded-circle" alt=""></a>

                    <div class="aside-alert-link">
                        <a href="javascript://" class="new" data-toggle="tooltip" title="You have 2 unread messages"><i data-feather="message-square"></i></a>
                        <a href="javascript://" class="new" data-toggle="tooltip" title="You have 4 new notifications"><i data-feather="bell"></i></a>
                        <a href="javascript://" data-toggle="tooltip" title="Sign out"><i data-feather="log-out"></i></a>
                    </div>
                </div>

                <div class="aside-loggedin-user">
                    <a href="javascript://" class="d-flex align-items-center justify-content-between mg-b-2" data-toggle="collapse">
                        <h6 class="tx-semibold mg-b-0">{{fullName}}</h6>
                        <i data-feather="chevron-down"></i>
                    </a>

                    <p class="tx-color-03 tx-12 mg-b-0">{{position}}</p>
                </div>

                <div class="collapse" id="loggedinMenu">
                    <ul class="nav nav-aside mg-b-0">
                        <li class="nav-item"><a href="javascript://" class="nav-link"><i data-feather="edit"></i> <span>Edit Profile</span></a></li>
                        <li class="nav-item"><a href="javascript://" class="nav-link"><i data-feather="user"></i> <span>View Profile</span></a></li>
                        <li class="nav-item"><a href="javascript://" class="nav-link"><i data-feather="settings"></i> <span>Account Settings</span></a></li>
                        <li class="nav-item"><a href="javascript://" class="nav-link"><i data-feather="help-circle"></i> <span>Help Center</span></a></li>
                        <li class="nav-item"><a href="javascript://" class="nav-link"><i data-feather="log-out"></i> <span>Sign Out</span></a></li>
                    </ul>
                </div>
            </div><!-- aside-loggedin -->

            <ul class="nav nav-aside">
                <div class="divider-text">WORKSPACES</div>

                <!-- Workspace Search -->
                <div class="search-form mb-3">
                    <input id="search-workspace" type="search" class="form-control" placeholder="Search all workspaces">
                </div>

                <li class="nav-item"><a href="/defi360" class="nav-link"><i data-feather="globe"></i> <span>DeFi Admin <small class="ml-1">[ Management ]</small></span></a></li>

                <li class="nav-item"><a href="/defi360" class="nav-link"><i data-feather="umbrella"></i> <span>DeFi Project <small class="ml-1">[ Collaborate ]</small></span></a></li>

                <li class="nav-item active"><a href="/telr" class="nav-link"><i data-feather="speaker"></i> <span>Telr <small class="ml-1">[ ATMs | POSs ]</small></span></a></li>

                <router-view name="sidebar" />

                <div class="divider-text">aBitSuite v20.1.3</div>

                <div class="text-center">
                    <small>
                        Brought to you by Modenero Corp
                        <br />&copy; 2020. All rights reserved.
                    </small>
                </div>
            </ul>
        </div>
    </aside>
</template>

<script>
export default {
    data: () => {
        return {
            workspaces: [],

            fullName: 'Katherine Pechon',
            position: 'Senior Blockchain Developer',
        }
    },
    methods: {
        /**
         * Type Ahead
         */
        initTypeAhead () {
            /**
             * Substring Matcher
             */
            const substringMatcher = () => {
                /* Localise this. */
                const self = this

                /* Return data to caller. */
                return function findMatches(q, cb) {
                    // an array that will be populated with substring matches
                    const matches = []

                    // regex used to determine if a string contains the substring `q`
                    const substrRegex = new RegExp(q, 'i')

                    // iterate through the pool of strings and for any string that
                    // contains the substring `q`, add it to the `matches` array
                    $.each(self.workspaces, function (i, _obj) {
                        const str = _obj.title

                        if (substrRegex.test(str)) {
                            matches.push(_obj)
                        }
                    })

                    /* Return matches. */
                    cb(matches)
                }
            }

            /* Initialize workspaces (using Bloodhound engine). */
            const bloodhound = new Bloodhound({
                datumTokenizer: Bloodhound.tokenizers.obj.whitespace('title'),
                queryTokenizer: Bloodhound.tokenizers.whitespace,
                // NOTE: We prefetch the TOP100 workspaces locally.
                prefetch: {
                    url: './assets/data/workspaces.json',
                    cache: false,
                },
            })

            /* Initialize type ahead. */
            $('#search-workspace').typeahead({
                hint: true,
                highlight: true,
                minLength: 1
            }, {
                name: 'workspaces',
                display: 'title',
                source: substringMatcher(),
                // source: bloodhound,
                templates: {
                    /* No results found. */
                    empty: [
                        '<div class="d-flex justify-content-center tx-medium tx-danger">',
                        'no workspaces found',
                        '</div>'
                    ].join('\n'),

                    /* Search suggestion. */
                    suggestion: Handlebars.compile(
                        [
                            '<div class="d-flex justify-content-between">',
                            '<strong>#{{title}}</strong> <small>[ {{members}} ]</small>',
                            '</div>'
                        ].join('\n')
                    ),

                },
            })
        },
    },
    mounted: async function () {
        console.log('Sidebar is mounted!')

        /* Load (local) data. */
        const data = await fetch('./assets/data/workspaces.json')

        /* Set (local) workspaces. */
        this.workspaces = await data.json()

        /* Initialize type ahead. */
        this.initTypeAhead()
    },
}
</script>

<style scoped>
/* TODO */
</style>
