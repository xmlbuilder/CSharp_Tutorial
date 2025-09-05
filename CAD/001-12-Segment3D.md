## Segment3D

```csharp
using System;
using System.Diagnostics;

namespace Geometry
{
  [Serializable]
  [DebuggerDisplay("P0=({P0.X}, {P0.Y}, {P0.Z}), P1=({P1.X}, {P1.Y}, {P1.Z})")]
  public class Segment3D : ICloneable
  {
    public enum segmentIntersectionType
    {
      Disjoint,
      EndPointTouch,
      Touch,
      Cross,
      OverlapInSegment,
      CollinearEndPointTouch,
    }

    public Point3D P0;
    public Point3D P1;

    #region Ctors

    public Segment3D()
    {
      P0 = new Point3D();
      P1 = new Point3D();
    }

    public Segment3D(Point3D p0, Point3D p1)
    {
      P0 = p0 ?? throw new ArgumentNullException(nameof(p0));
      P1 = p1 ?? throw new ArgumentNullException(nameof(p1));
    }

    public Segment3D(Vector3D p1)
    {
      P0 = Point3D.Origin;
      P1 = new Point3D(p1.X, p1.Y, p1.Z);
    }

    public Segment3D(double x0, double y0, double z0, double x1, double y1, double z1)
    {
      P0 = new Point3D(x0, y0, z0);
      P1 = new Point3D(x1, y1, z1);
    }

    protected Segment3D(Segment3D other)
    {
      P0 = (Point3D)other.P0.Clone();
      P1 = (Point3D)other.P1.Clone();
    }

    public virtual object Clone() => new Segment3D(this);

    #endregion

    #region Convenience

    /// <summary>세그먼트가 사실상 점인가?</summary>
    public bool IsPoint => P0.Equals(P1);

    /// <summary>중점.</summary>
    public Point3D MidPoint => Point3D.MidPoint(P0, P1);

    /// <summary>길이.</summary>
    public double Length => Point3D.Distance(P0, P1);

    /// <summary>길이 제곱.</summary>
    public double LengthSquared
    {
      get
      {
        double dx = P1.X - P0.X;
        double dy = P1.Y - P0.Y;
        double dz = P1.Z - P0.Z;
        return dx * dx + dy * dy + dz * dz;
      }
    }

    /// <summary>방향 벡터(P1 - P0).</summary>
    public Vector3D Direction => new Vector3D(P1.X - P0.X, P1.Y - P0.Y, P1.Z - P0.Z);

    public static implicit operator Vector3D(Segment3D s)
      => new Vector3D(s.P1.X - s.P0.X, s.P1.Y - s.P0.Y, s.P1.Z - s.P0.Z);

    #endregion

    #region Basics

    /// <summary>
    /// 외부 점을 직선(세그먼트 연장선)에 사영한 파라미터 t 반환.
    /// (수치 안정성을 위해 더 가까운 끝점을 기준으로 계산하는 원 구현을 유지)
    /// </summary>
    public double Project(Point3D pt)
    {
      var v = Vector3D.Subtract(P1, P0);
      double len2 = v.LengthSquared;
      if (len2 <= 0.0) return 0.0;

      // 원 코드: 가까운 쪽 기준으로 계산하여 소수점 오차를 줄임
      if (pt.DistanceTo(P0) > pt.DistanceTo(P1))
      {
        return 1.0 + Vector3D.Dot(pt - P1, v) / len2;
      }
      return Vector3D.Dot(pt - P0, v) / len2;
    }

    /// <summary>점에 대한 세그먼트 최근접 파라미터(0..1 클램프).</summary>
    public double ClosestPointTo(Point3D pt)
    {
      double t = Project(pt);
      if (t < 0.0) t = 0.0;
      else if (t > 1.0) t = 1.0;
      return t;
    }

    /// <summary>세그먼트 상의 매개변수 t(0..1)에 해당하는 점.</summary>
    public Point3D PointAt(double t)
    {
      double s = 1.0 - t;
      var a = P0; var b = P1;
      return new Point3D(
        a.X == b.X ? a.X : s * a.X + t * b.X,
        a.Y == b.Y ? a.Y : s * a.Y + t * b.Y,
        a.Z == b.Z ? a.Z : s * a.Z + t * b.Z
      );
    }

    /// <summary>특정 점까지 세그먼트를 앞/뒤로 연장.</summary>
    public void ExtendTo(Point3D extPt)
    {
      double t = Project(extPt);
      if (t > 1.0)      P1 = PointAt(t);
      else if (t < 0.0) P0 = PointAt(t);
      // 0..1이면 변화 없음
    }

    /// <summary>주어진 축(center, dir) 위에 세그먼트 두 끝점이 모두 존재하는가?</summary>
    public bool IsOnAxis(Vector3D dir, Point3D center)
    {
      Point3D axisEnd = center + dir;
      var axis = new Segment3D(center, axisEnd);

      Point3D prj0 = P0.ProjectTo(axis);
      Point3D prj1 = P1.ProjectTo(axis);
      return prj0.Equals(P0) && prj1.Equals(P1);
    }

    /// <summary>세그먼트가 주어진 평면 위(거리 tol 이하)에 있는가?</summary>
    public bool IsInPlane(Plane plane, double tol)
    {
      return Math.Abs(plane.DistanceTo(P0)) <= tol &&
             Math.Abs(plane.DistanceTo(P1)) <= tol;
    }

    #endregion

    #region Plane / Triangle Intersections

    public bool IntersectWith(Plane plane, out Point3D intPoint)
      => IntersectWithInternal(plane.Equation, out intPoint, computeIntersection: true, infinite: false);

    public bool IntersectWith(Plane plane, bool infinite, out Point3D intPoint)
      => IntersectWithInternal(plane.Equation, out intPoint, computeIntersection: true, infinite: infinite);

    public bool IntersectWith(PlaneEquation pe)
      => IntersectWithInternal(pe, out Point3D _, computeIntersection: false, infinite: false);

    public bool IntersectWith(PlaneEquation pe, bool infinite)
      => IntersectWithInternal(pe, out Point3D _, computeIntersection: false, infinite: infinite);

    public bool IntersectWith(PlaneEquation pe, out Point3D intPoint)
      => IntersectWithInternal(pe, out intPoint, computeIntersection: true, infinite: false);

    public bool IntersectWith(PlaneEquation pe, bool infinite, out Point3D intPoint)
      => IntersectWithInternal(pe, out intPoint, computeIntersection: true, infinite: infinite);

    /// <summary>
    /// 평면 방정식과의 교차(직선으로 볼지 세그먼트로 제한할지 선택).
    /// </summary>
    public bool IntersectWithInternal(PlaneEquation pe, out Point3D intPoint, bool computeIntersection, bool infinite)
    {
      const double tol = 1e-12;
      intPoint = null;

      Vector3D dir = Vector3D.Subtract(P1, P0);
      double denom = ((Vector3D)pe) * dir; // N · dir

      if (Math.Abs(denom) < 1e-15) return false; // 평행

      double t = -pe.ValueAt(P0) / denom;        // P0에서의 파라미터
      if (!infinite && (t < -tol || t > 1.0 + tol)) return false;

      if (computeIntersection) intPoint = P0 + t * dir;
      return true;
    }

    public bool IntersectWith(Point3D p1, Point3D p2, Point3D p3, out Point3D intPoint)
      => IntersectWith(p1, p2, p3, ray: false, out intPoint);

    private bool IntersectWith(Point3D a, Point3D b, Point3D c, bool ray, out Point3D hit)
      => IntersectWith(a, b, c, ray, 0.0, 1.0, out double _, out double _, out hit);

    /// <summary>
    /// 삼각형(a,b,c)과의 교차. ray==true면 무한 레이, false면 세그먼트.
    /// s,t는 삼각형 (a,b,c) 내의 두 배리센트 좌표(세 번째는 1-s-t).
    /// </summary>
    public bool IntersectWith(Point3D a, Point3D b, Point3D c, bool ray, out Point3D intPoint, out double s, out double t)
      => IntersectWith(a, b, c, ray, -Utility.TOL9, 1.0 + Utility.TOL9, out s, out t, out intPoint);

    private bool IntersectWith(
      Point3D a, Point3D b, Point3D c, bool asRay,
      double baryMin, double baryMax,
      out double s, out double t, out Point3D hit)
    {
      s = t = -1.0;
      hit = null;

      // 삼각형 평면 법선
      Vector3D ab = new Vector3D(a, b);
      Vector3D ac = new Vector3D(a, c);
      Vector3D n  = Vector3D.Cross(ab, ac);
      if (n.LengthSquared < 1e-20) return false; // 퇴화 삼각형

      // 세그먼트 방향
      Vector3D d = new Vector3D(P0, P1);
      double nDotD = n * d;
      if (Math.Abs(nDotD) < 1e-15) return false; // 평행

      // 교차 파라미터(세그먼트/레이 기준)
      Vector3D aToP0 = new Vector3D(a, P0);
      double tLine = -(n * aToP0) / nDotD;
      if (tLine < 0.0 || (!asRay && tLine > 1.0)) return false;

      hit = P0 + tLine * d;

      // 배리센트 계산
      double aa = ab * ab;
      double abac = ab * ac;
      double cc = ac * ac;

      Vector3D aToHit = new Vector3D(a, hit);
      double ah_ab = aToHit * ab;
      double ah_ac = aToHit * ac;

      double det = abac * abac - aa * cc;
      s = (abac * ah_ac - cc * ah_ab) / det;
      if (s < baryMin || s > baryMax) return false;

      t = (abac * ah_ab - aa * ah_ac) / det;
      return t >= baryMin && (s + t) <= baryMax;
    }

    #endregion

    #region AABB (box) Intersection

    /// <summary>
    /// AABB(boxMin, boxMax)와의 교차. infinite=true면 무한 직선으로, false면 세그먼트로 처리.
    /// </summary>
    public bool IntersectWith(Point3D boxMin, Point3D boxMax, bool infinite = false)
    {
      Vector3D dir = new Vector3D(P0, P1);
      Vector3D invDir = new Vector3D(1.0 / dir.X, 1.0 / dir.Y, 1.0 / dir.Z);

      int[] sign = {
        invDir.X < 0.0 ? 1 : 0,
        invDir.Y < 0.0 ? 1 : 0,
        invDir.Z < 0.0 ? 1 : 0
      };

      double tMin, tMax;
      if (infinite)
      {
        tMin = double.NegativeInfinity;
        tMax = double.PositiveInfinity;
      }
      else
      {
        // 세그먼트 파라미터 범위
        tMin = Project(P0); // 보통 0
        tMax = Project(P1); // 보통 1
      }

      return IntersectWithInternal(sign, boxMin, boxMax, invDir, tMin, tMax);
    }

    public bool IntersectWithInternal(int[] sign, Point3D boxMin, Point3D boxMax, Vector3D invDir, double tMin, double tMax)
    {
      Point3D origin = P0;
      Point3D[] bounds = { boxMin, boxMax };

      double txMin = (bounds[sign[0]].X     - origin.X) * invDir.X;
      double txMax = (bounds[1 - sign[0]].X - origin.X) * invDir.X;

      double tyMin = (bounds[sign[1]].Y     - origin.Y) * invDir.Y;
      double tyMax = (bounds[1 - sign[1]].Y - origin.Y) * invDir.Y;

      if (txMin > tyMax || tyMin > txMax) return false;
      if (tyMin > txMin) txMin = tyMin;
      if (tyMax < txMax) txMax = tyMax;

      double tzMin = (bounds[sign[2]].Z     - origin.Z) * invDir.Z;
      double tzMax = (bounds[1 - sign[2]].Z - origin.Z) * invDir.Z;

      if (txMin > tzMax || tzMin > txMax) return false;
      if (tzMin > txMin) txMin = tzMin;
      if (tzMax < txMax) txMax = tzMax;

      return txMin < tMax && txMax > tMin;
    }

    #endregion

    #region Segment-Segment Intersection

    /// <summary>두 세그먼트의 최근접 점 (무한/세그먼트 모드) 계산.</summary>
    public static bool Intersection(Segment3D segA, Segment3D segB, bool infinite, out Point3D pointOnA, out Point3D pointOnB)
      => Intersection(segA, segB, infinite, out pointOnA, out pointOnB, out double _, out double _);

    public static bool Intersection(Segment3D segA, Segment3D segB, bool infinite, out Point3D pointOnA, out Point3D pointOnB, out double paramOnA, out double paramOnB)
    {
      pointOnA = null; pointOnB = null;
      paramOnA = paramOnB = 0.0;

      Point3D a0 = segA.P0, a1 = segA.P1;
      Point3D b0 = segB.P0, b1 = segB.P1;

      // 끝점 동일성(수치오차 허용) 빠른 체크
      int coincidentCount = 0;
      if (isNormalCheck(a0, b0, 0.0, 0.0, ref pointOnA, ref pointOnB, out paramOnA, out paramOnB)) ++coincidentCount;
      if (isNormalCheck(a1, b0, 1.0, 0.0, ref pointOnA, ref pointOnB, out paramOnA, out paramOnB)) ++coincidentCount;
      if (isNormalCheck(a0, b1, 0.0, 1.0, ref pointOnA, ref pointOnB, out paramOnA, out paramOnB)) ++coincidentCount;
      if (isNormalCheck(a1, b1, 1.0, 1.0, ref pointOnA, ref pointOnB, out paramOnA, out paramOnB)) ++coincidentCount;

      switch (coincidentCount)
      {
        case 1:
        case 3:
        case 4:
          return true;  // 최소 한 쌍의 동일 끝점
        case 2:
          return false; // 모호한 케이스(두 쌍만 동일) -> 원 구현 유지
      }

      // 일반 해 (Skew/교차/평행)
      Vector3D u = a1 - a0;
      Vector3D v = b1 - b0;
      Vector3D w0 = a0 - b0;

      double a = Vector3D.Dot(u, u); // ||u||^2
      double b = Vector3D.Dot(u, v); // u·v
      double c = Vector3D.Dot(v, v); // ||v||^2
      double d = Vector3D.Dot(u, w0);
      double e = Vector3D.Dot(v, w0);

      double denom = a * c - b * b;
      if (Math.Abs(denom) > 0.0)
      {
        double s = (b * e - c * d) / denom;
        double t = (a * e - b * d) / denom;

        paramOnA = s;
        paramOnB = t;

        if (infinite)
        {
          pointOnA = a0 + s * u;
          pointOnB = b0 + t * v;
          return true;
        }

        if (s < 0.0 || s > 1.0 || t < 0.0 || t > 1.0) return false;

        pointOnA = a0 + s * u;
        pointOnB = b0 + t * v;
        return true;
      }

      // 평행(denom == 0) 또는 퇴화: 원 코드 로직을 유지(보수적 false)
      paramOnA = paramOnB = 0.0;
      return false;
    }

    private static bool isNormalCheck(Point3D a, Point3D b, double sA, double sB, ref Point3D onA, ref Point3D onB, out double tA, out double tB)
    {
      tA = tB = 0.0;
      if (!Utility.AreEqual(a.X, b.X, 1.0) ||
          !Utility.AreEqual(a.Y, b.Y, 1.0) ||
          !Utility.AreEqual(a.Z, b.Z, 1.0))
        return false;

      onA = a;
      onB = b;
      tA = sA;
      tB = sB;
      return true;
    }

    #endregion

    #region Collinearity / Overlap

    public static bool AreCollinear(Segment3D s1, Segment3D s2)
    {
      var u = (Vector3D)s1; u.Normalize();
      var v = (Vector3D)s2; v.Normalize();

      if (Vector3D.AreParallel(u, v))
      {
        var w = new Vector3D(s1.P0, s2.P0);
        if (Utility.AreEqual(0.0, w.Length, 1.0)) return true;
        w.Normalize();
        if (Vector3D.AreParallel(w, v)) return true;
      }
      return false;
    }

    public static bool AreOverlapping(Segment3D s1, Segment3D s2)
    {
      double tol = 1e-12 * Math.Min(s1.Length, s2.Length);

      var u = (Vector3D)s1; u.Normalize();
      var v = (Vector3D)s2; v.Normalize();
      if (!Vector3D.AreParallel(u, v)) return false;

      double t0 = s2.Project(s1.P0);
      double t1 = s2.Project(s1.P1);
      if (t0 > t1) Utility.Swap(ref t0, ref t1);

      bool leftOK  = t1 >= tol;
      bool rightOK = t0 <= 1.0 - tol;

      if (!(leftOK && rightOK)) return false;

      // 실제 겹치는 점이 존재하는지 최종 체크
      var q = s2.PointAt(Math.Clamp(0.5 * (Math.Max(0.0, t0) + Math.Min(1.0, t1)), 0.0, 1.0));
      return Utility.Compare(q.DistanceTo(s1.P0), 0.0, 1e-12) < 0;
    }

    #endregion

    public override string ToString()
      => $"Segment3D({P0}, {P1})";
  }
}
```