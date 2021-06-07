<script>
    import * as urljoin from "url-join";

    export default {
        props: {
            baseHref: {
                type: String,
                default: "#"
            },
            argsHref: {
                type: Array,
                default(){
                    return [];
                }
            }
        },
        methods: {
            /**
             *
             * @param {array} pathAndOrRoot
             */
            routeTo(...pathAndOrRoot){
                let root = typeof pathAndOrRoot[pathAndOrRoot.length -1] === "boolean" ? pathAndOrRoot.pop() : false,
                    basePath = "#accueil",
                    path = []
                ;

                pathAndOrRoot = pathAndOrRoot.flat();

                for(let value of pathAndOrRoot){
                    if(isNaN(value) || value === undefined || value === null) continue;
                    if(value.includes(".")) {
                        value.replace('.', '');
                    }
                    path.push(value);
                }

                // path = String(path);

                if(!root) {
                    basePath = this.baseHref;
                    if (basePath[0] !== '#') {
                        basePath = '#' + basePath;
                    }
                }

                // if only #, do not add '/' between # and the first element of path
                if(basePath.length === 1){
                    basePath += path.shift();
                }

                return urljoin(basePath, ...path);
            },
            /**
             *
             * @param {string | array} path
             * @param {boolean} root
             */
            redirectTo(path, root = false){
                window.location.hash = this.routeTo(path, root);
            }
        },
        computed: {
            // route(){
            //     return window.location.hash.split('#')[0];
            // },
            // routeArray(){
            //     return this.route.split('/');
            // }
        }
    }
</script>
