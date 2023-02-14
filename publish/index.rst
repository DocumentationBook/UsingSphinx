.. _resources_pipeline:

Publishing
##########


On premise publishing
=====================


Publishing to GitHub Pages
==========================

GitHub Pages is a documentation hosting service that enables you to publish your documentation on a separate subdomain
in the ``github.io`` domain. It may look as follows:

*  ``<your-organization-name>.github.io>``
*  ``<your-github-login-name>.github.io>``

It accepts a built documentation set, that is, all HTML, CSS, and JavaScript files compiled and ready to publish,
or you can configure a special process.

.. note:: To use this hosting, you need to have a GitHub account and have your documentation project stored in a
   `GitHub <https://github.com>`_ repository synced with your local git repository.

Pay attention to the following GitHub Pages specifics when you publish compiled HTML documents:

*  It imports data only from the root folder or from the ``docs/`` folder in a GitHub repository.
*  GitHub Pages does not import folders, which names start with an underscore.
   For example, Sphinx copies images to the ``_images/`` folder and uses the ``_static/`` to store CSS, JavaScript and
   other files. One work around is to rename those folders and all links to them in all HTML files before you publish
   the documentation. This requires additional configuration steps.

The following steps help you manage these specifics:

#. In the ``Makefile`` file, add one more destination for the compiled HTML documents, for example::

      GITPAGES      = docs

#. In the ``Makefile`` file, add a rule for the new type of building, for example called ``pages``::

      pages:
         @rm -rf docs
         @$(SPHINXBUILD) -b dirhtml "$(SOURCEDIR)" "$(GITPAGES)" $(SPHINXOPTS) $(O)
         @./postbuild_gitpages.sh

   With this rule, the ``makefile`` utility will do the following:

   *  Remove the ``docs/`` folder containing HTML documents from the previous build.

      .. note:: It means you have to rebuild the whole documentation, which may take substantial time.
         Therefore, it's better to fix all problems locally using the typical procedure before you start this process.

   *  Run the ``sphinx-build`` command using the newly assigned destination path.
   *  Run the script (see the next step) to make necessary substitutions.

#. Create a shell script, for example, ``postbuild_gitpages.sh`` with the following contents::

      for file in $(find docs -name "*.html")
      do
          echo $file
          sed -i '' -e 's@_images/@images/@g' -e 's@_static/@static/@g' $file
      done

      mv docs/_static docs/static
      mv docs/_images docs/images
      echo _static/ and _images/ are renamed to static/ and images/.

   This script performs the following operations:

   *  In the ``for`` loop, open every HTML file and inside it remove the underscore prefix
      from words ``_images`` and ``_static``.

      .. note:: This sets a restriction on using these words in your documents.

   *  Remove the underscore prefix from folder names ``_images/`` and ``_static/``.

#. In the ``.gitignore`` file, add the following files and folders that are not part of the HTML documentation::

      .buildinfo, .doctrees, _plantuml, objects.inv

After completing all these steps, you can run the build-publish process as follows:

#. Run the building process::

      $ make pages

#. Add and commit the updates::

      $ git add .
      $ git commit -m "Your text message."
      $ git push

#. Follow the `GitHub Pages tutorial <https://docs.github.com/en/pages>`_ to publish
   your documentation from the ``doc/`` folder in your repository on GitHub.
   You can do it using the **Pages** item in the repository **Settings** menu.


Publishing to Read the Docs
===========================



Additional resources
====================

* `UnlockedEdu/documentation-pipeline-generator <https://github.com/UnlockedEdu/documentation-pipeline-generator>`_ and
   a separate `documentation site <https://unlockededu.github.io/documentation-pipeline-generator/>`_
*  `Docs-as-code pipeline on GitLab using Sphinx and Docker <https://gitlab.com/papercut-docs-as-code/docs-as-code>`_
*  `Treat Docs Like Code: GitHub and Sphinx <https://www.docslikecode.com/>`_
*  `GitHub Pages <https://docs.github.com/en/pages>`_
*  `Read the Docs Tutorial <https://docs.readthedocs.io/en/stable/tutorial/>`_