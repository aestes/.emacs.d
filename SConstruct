import os
import platform

from SCons.Script import ARGUMENTS, Variables, Environment, Alias, Default

if platform.system() == 'Darwin':
    emacs = '/Applications/Emacs.app/Contents/MacOS/Emacs'
else:
    emacs = 'emacs'

vars = Variables()
vars.Add('emacs', default=ARGUMENTS.get('emacs', emacs))
vars.Add('init_dir', default=ARGUMENTS.get('init-dir', os.path.abspath('.')))
env = Environment(ENV=dict(os.environ,
                           PATH=':'.join(['emacs-env/bin', os.environ['PATH']])),
                  variables=vars)

org_file = 'init.org'

el, = env.Command(
    target='init.el',
    source=org_file,
    action=('$emacs --batch --eval '
            '\'(progn (require (quote package))(package-initialize)'
            '(find-file "$SOURCE")(org-mode)(print org-version)(org-babel-tangle))\'')
)
Alias('tangle', el)
Default(el)

html, = env.Command(
    target='html/index.html',
    source=org_file,
    action=('org-export/org-export html '
            '--package-dir $init_dir '
            '--infile $SOURCE --outfile $TARGET '
            '--bootstrap --embed-css')
)
Alias('html', html)

publish, = env.Command(
    target='ghp-import.log',
    source=html,
    action='ghp-import -p html > $TARGET'
)
Alias('publish', publish)
