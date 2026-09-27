# Tool Pocket Generator V135

## Release Notes

### V135 — Correctly construct consecutive Curve-point sections

**Source and provenance:** Built directly from the supplied V134 FINAL ZIP (`Tool_Pocket_Generator_V134_FINAL.zip`). The V134 archive was inspected and used as the immediate source. No older build was substituted.

**Problem:** V134 allowed adjacent points to be set to Curve, but the display and sampled-profile builders still assumed a Curve point was isolated between Straight points. Consecutive Curve points could therefore be skipped or connected incorrectly, producing a large looping/misleading outline.

**Fix:** Updated both the canvas outline renderer and the sampled profile builder to use the same run-based construction. Between two Straight anchor points:
- no Curve points: draw a straight point-to-point segment;
- one Curve point: retain the existing single quadratic curve behaviour;
- multiple consecutive Curve points: construct a chain of quadratic spans, one per Curve point, meeting at midpoints between neighbouring controls and terminating at the next Straight anchor.
This keeps the outline connected in point order and makes adjacent Curve selections usable. If every point is Curve and there are no Straight anchors, the outline falls back to straight point-to-point connections so the profile remains visible and connected.

**Process/order:** Create/import outline → choose any points as Curve or Straight (including adjacent Curve points) → drag Curve points → confirm profile editing → close profile → Geometry Check → export JSON/STL.

**What was not changed:** Point-type selection freedom, point identity/UID handling, JSON import/export data mapping, photo loading, physical-grid confirmation, exact outline detection, workspace, point add/insert/delete/undo, profile closure, geometry-check rules, STL construction, clearance/depth settings, and download behavior. The isolated one-Curve quadratic behaviour is retained.

**Verification performed:** Confirmed V134 contained the faulty isolated-point assumptions in both `sampled()` and `drawProfilePath()`. Replaced both with matching run-based handling. Checked both inline JavaScript blocks with Node syntax validation and checked ZIP integrity.

**Verification not performed:** No browser canvas interaction, geometric fixture test, iPad/Safari test, Geometry Check run on the user's profile, or STL slicing/printing test was performed. User retest is required, especially for a sequence of 2+ adjacent Curve points and for all-Curve profiles.
