# How to get an issue triaged 
* *V8 tracker*: Set the state to `Untriaged`
* *Chromium tracker*: Set the state to `Untriaged` and add the component `Blink>JavaScript`

# How to assign V8 issues in the Chromium tracker
Please assign issues to the V8 specialty sheriffs of one of the
following categories:

  * Stability: jkummerow@c....org, adamk@c....org
  * Performance: bmeurer@c....org, mvstanton@c....org
  * Clusterfuzz: Set the bug to the following state:
    * `label:ClusterFuzz component:Blink>JavaScript status:Available -has:owner`
    * Will show up in [this](https://bugs.chromium.org/p/chromium/issues/list?can=2&q=label%3AClusterFuzz+component%3ABlink%3EJavaScript+status%3AAvailable+-has%3Aowner) query.
    * CC mstarzinger@c....org and ishell@c....org

Please CC hablich@c....org on all issues.

Assign remaining issues to hablich@c....org.

Use the component `Blink>JavaScript` on all issues.

**Please note that this only applies to issues tracked in the Chromium issue tracker.**