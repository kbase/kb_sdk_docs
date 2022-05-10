Developer Guidelines
====================

The KBase SDK is intended to lower the barrier to integrating analysis
tools as KBase apps and enable the broader community to more easily
contribute to KBase. Tools submitted for addition to KBase will go
through a standard release process (Development, Beta and Release).

App Environments
-----------------

1. **Development:** Apps in the “Development” state will be limited in
   visibility. They will be visible in a publicly accessible environment
   but won’t run against the production data stores and can’t be used in
   production Narratives (i.e., the app will not be visible at
   narrative.kbase.us).
2. **Beta:** Apps in “Beta” will be visible to all users in the production
   Narrative Interface (narrative.kbase.us), if they choose to view Beta
   apps (by changing the R (for Release) in the Apps panel to B).
3. **Release:** Methods in the Release state will be visible to all.

In order to transition an app from Development to Beta and, finally,
Release, the app must undergo a review by KBase staff. This review is
intended to ensure that the app meets a minimal set of guidelines and
standards. Here are the general criteria that will be used when
reviewing new apps which have been requested to be promoted to a beta or
release state.

Criteria:
---------

1. All components of the tool are licensed for unrestricted open source
   use--for example, they cannot be “Free for academic use (but
   commercial users must pay to use it)”. The finished app should use an |open_source_license_link|. We recommend the |mit_license_link|.
2. Documentation for app is clear and includes any appropriate
   references (e.g., published papers on a tool or method) - See  |documentation_link|.
3. The app includes a public example Narrative demonstrating the app in
   action on real data.
4. The repository contains minimal tests (e.g., at least one unit test
   per app with some coverage of important parameters and inputs).
   Ideally 70% code coverage for testing, but this is an ideal and not a
   hard requirement
5. All of the tests above must pass
6. Has the code been reviewed by KBase Implementation Team staff? A
   basic expectation is that for Python code is that flake8 shows no
   errors or warnings, see  |codeReview_link| 
   for details on flake8 configuration
7. Is the  |provenance_link|?
   As a rule, outputting your objects using the |DateFileUtils_link| 
   API will take care of filling out the provenance information. In
   general it is best to use |DateFileUtils_link| 
   whenever possible for input and output, |kbsdkvalidate_link| or  |kbsdkTest_link| 
8. Updates to released modules have incremental version numbers.
9. The apps have a category.
10. The App Catalog text, especiallyPublications, meets the guidance.
11. The Dockerfile has a designated MAINTAINER.

See also:  |KBaseproduct_link| 

Policy for App Deprecation
--------------------------

In KBase, we strive to maintain apps in our catalog in good standing. We
encourage all developers to keep their apps up-to-date and to check app run
success stats, debug logs, and introduce bug fixes when necessary. Keeping
documentation up-to-date is especially important, particularly when changes are
introduced to the app. User inquiry can also prompt changes to documentation.
Developers will be contacted about user inquiries via the Help Board. The KBase
team is committed to helping developers in the process of keeping their apps in
good standing.

If you feel that your app is out of date and you can no longer maintain it and
have not been able to find someone to take it over for you, then you should
contact KBase Help to start the depreciation process. If there is an issue with
your app, we may need to inactivate your app, but this will not be done without
contacting you first.

What prompts an app to be deprecated?
^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^

1. Low app run success rate (~50%).
2. A new version of the App is available in the literature noting bug fixes and/or improvements that impact the results.
3. App methodology is shown to be obsolete in the literature.
4. Current documentation is insufficient in answering user inquiries.
5. A newer version of type spec of the object that the App does not work for. Essentially, an App being out of date.

.. External links

.. |documentation_link| raw:: html

   <a href="https://github.com/kbase/project_guides/blob/master/Method_man_page.md" target="_blank">Method Man pages document</a>

.. |codeReview_link| raw:: html

   <a href="https://github.com/kbase/project_guides/blob/master/RecommendedEditors.md#flake8-configuration" target="_blank">this document</a>

.. |provenance_link| raw:: html

   <a href="https://github.com/kbase/KBaseDeveloperBootstrap/blob/master/Provenance_info.md" target="_blank">provenance information for output objects properly</a>

.. |DateFileUtils_link| raw:: html

   <a href="https://narrative.kbase.us/#catalog/modules/DataFileUtil" target="_blank">DataFileUtils</a>

.. |kbsdkvalidate_link| raw:: html

   <a href="https://github.com/kbaseapps/kb_Velvet/blob/master/.travis.yml" target="_blank">minimally performs a "kb-sdk validate"</a>

.. |kbsdkTest_link| raw:: html

   <a href="https://github.com/kbaseapps/kb_ballgown/blob/master/.travis.yml" target="_blank">ideally performs a "kb-sdk test"</a>

.. |KBaseproduct_link| raw:: html

   <a href="https://github.com/kbase/roadmap/blob/master/KBase%20product%20requirements.md" target="_blank">KBase product requirements</a>


.. |open_source_license_link| raw:: html

   <a href="https://opensource.org/licenses/alphabetical" target="_blank">open source license</a>


.. |mit_license_link| raw:: html

   <a href="https://github.com/kbase/license_template/blob/master/LICENSE.md" target="_blank">MIT License</a>

