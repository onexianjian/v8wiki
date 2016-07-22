# How to get an issue triaged 
* *V8 tracker*: Set the state to `Untriaged`
* *Chromium tracker*: Set the state to `Untriaged` and add the component `Blink>JavaScript`

# How to assign V8 issues in the Chromium tracker
Please move issues to the V8 specialty sheriffs queue of one of the
following categories:

  * Stability: `status=available component:Blink>JavaScript label:Stability -label:Clusterfuzz`
    * Will show up in [this](https://bugs.chromium.org/p/chromium/issues/list?can=2&q=status%3Davailable+component%3ABlink%3EJavaScript+label%3AStability+-label%3AClusterfuzz) query
  * Performance: hablich@c....org, mvstaton@c....org
  * Clusterfuzz: Set the bug to the following state:
    * `label:ClusterFuzz component:Blink>JavaScript status:Available -has:owner`
    * Will show up in [this](https://bugs.chromium.org/p/chromium/issues/list?can=2&q=label%3AClusterFuzz+component%3ABlink%3EJavaScript+status%3AAvailable+-has%3Aowner) query.
    * CC mstarzinger@c....org, ishell@c....org, rossberg@c....org, titzer@c....org

Assign remaining issues to hablich@c....org.

Use the component `Blink>JavaScript` on all issues.

**Please note that this only applies to issues tracked in the Chromium issue tracker.**