## Segmement2D

```csharp
using System;
using System.Diagnostics;
using System.Drawing;

namespace Geometry
{
  [Serializable]
  [DebuggerDisplay("P0=({P0.X}, {P0.Y}), P1=({P1.X}, {P1.Y})")]
  public class Segment2D : ICloneable
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
    // 원 코드와의 호환을 위해 필드 이름을 유지합니다.
    public Point2D P0;
    public Point2D P1;

    #region Ctors

    public Segment2D()
    {
      P0 = new Point2D(0.0, 0.0);
      P1 = new Point2D(0.0, 0.0);
    }

    public Segment2D(Point2D p0, Point2D p1)
    {
      P0 = p0 ?? throw new ArgumentNullException(nameof(p0));
      P1 = p1 ?? throw new ArgumentNullException(nameof(p1));
    }

    public Segment2D(Point p0, Point p1)
    {
      P0 = new Point2D(p0.X, p0.Y);
      P1 = new Point2D(p1.X, p1.Y);
    }

    public Segment2D(double x0, double y0, double x1, double y1)
    {
      P0 = new Point2D(x0, y0);
      P1 = new Point2D(x1, y1);
    }

    protected Segment2D(Segment2D other)
    {
      P0 = (Point2D)other.P0.Clone();
      P1 = (Point2D)other.P1.Clone();
    }

    public virtual object Clone() => new Segment2D(this);

    #endregion

    #region Convenience

    /// <summary>세그먼트 방향 벡터(P1-P0).</summary>
    public Vector2D Direction => Vector2D.Subtract(P1, P0);

    /// <summary>세그먼트 길이.</summary>
    public double Length => Point2D.Distance(P0, P1);

    /// <summary>중점.</summary>
    public Point2D MidPoint => Point2D.MidPoint(P0, P1);

    /// <summary>
    /// 좌측 법선(방향 벡터를 +90° 회전). 원 코드와 동일하게 정규화하지 않습니다.
    /// 필요 시 Normalize() 호출.
    /// </summary>
    public Vector2D Normal => new Vector2D(-(P1.X - P0.X), P1.Y - P0.Y);

    /// <summary>세그먼트가 사실상 점인가?</summary>
    public bool IsPoint
    {
      get
      {
        // Point2D.Equals는 Utility.Compare 기반이라 수치 오차에 강건
        return P0.Equals(P1);
      }
    }

    /// <summary>정규화된 접선 벡터.</summary>
    public Vector2D Tangent
    {
      get
      {
        var t = Direction;
        t.Normalize();
        return t;
      }
    }

    // 원 API 유지
    public Vector2D Tan() => Tangent;

    #endregion

    #region Operators / Cast

    public static implicit operator Vector2D(Segment2D s)
      => new Vector2D(s.P1.X - s.P0.X, s.P1.Y - s.P0.Y);

    #endregion

    #region Geometry

    /// <summary>
    /// 점을 세그먼트의 매개변수 t(0..1)에 직교 투영. 구간 외의 값도 반환될 수 있음.
    /// </summary>
    public double Project(Point2D pt)
    {
      var v = Direction;
      double len2 = v.LengthSquared;
      if (len2 <= 1e-18) return 0.0; // 퇴화 구간(점)
      var w = Vector2D.Subtract(pt, P0);
      return Vector2D.Dot(w, v) / len2;
    }

    /// <summary>점에서 세그먼트에 가장 가까운 매개변수(0..1) 반환.</summary>
    public double ClosestPointTo(Point2D pt)
    {
      double t = Project(pt);
      if (t < 0.0) t = 0.0;
      else if (t > 1.0) t = 1.0;
      return t;
    }

    /// <summary>세그먼트 상의 매개변수 t(0..1)에 해당하는 점.</summary>
    public Point2D PointAt(double t)
    {
      double s = 1.0 - t;
      return new Point2D(s * P0.X + t * P1.X, s * P0.Y + t * P1.Y);
    }

    /// <summary>세그먼트를 좌측 법선 방향으로 amount만큼 평행 이동.</summary>
    public Segment2D Offset(double amount)
    {
      var dir = Direction;
      if (!dir.Normalize()) return new Segment2D((Point2D)P0.Clone(), (Point2D)P1.Clone());
      var n = new Vector2D(dir.Y, -dir.X) * amount;
      return new Segment2D(P0 + n, P1 + n);
    }

    /// <summary>시작/끝을 각각 atStart/atEnd만큼 연장(+)/단축(-).</summary>
    public void ExtendBy(double atStart, double atEnd)
    {
      var t = Direction;
      if (!t.Normalize()) return;
      if (atStart != 0.0)
      {
        var p = P0 - atStart * t;
        P0.X = p.X; P0.Y = p.Y;
      }
      if (atEnd != 0.0)
      {
        var p = P1 + atEnd * t;
        P1.X = p.X; P1.Y = p.Y;
      }
    }

    #endregion

    #region Intersections

    // 내부: 두 벡터를 정규화하여 외적(=수직 내적) 값을 반환
    private static double PerpDotOfNormalized(double[] a, double[] b, out double[] na, out double[] nb)
    {
      na = new double[2];
      nb = new double[2];

      double la = Math.Sqrt(a[0] * a[0] + a[1] * a[1]);
      if (la > 2.2250738585072014E-308) { na[0] = a[0] / la; na[1] = a[1] / la; }

      double lb = Math.Sqrt(b[0] * b[0] + b[1] * b[1]);
      if (lb > 2.2250738585072014E-308) { nb[0] = b[0] / lb; nb[1] = b[1] / lb; }

      return na[0] * nb[1] - na[1] * nb[0];
    }

    // 내부: 공선(parallel & collinear)일 때 s2 파라미터 구간의 겹침 [t0,t1]을 계산
    private static bool ComputeCollinearOverlapParams(
      Segment2D a, Segment2D b,
      double[] dirB, double[] a0MinusB0, double[] nPerp,
      out double t0, out double t1)
    {
      // a.P1 - b.P0
      double[] a1MinusB0 = { a.P1.X - b.P0.X, a.P1.Y - b.P0.Y };

      if (Math.Abs(nPerp[0]) > 0.7071067811865476) // 축 안정성
      {
        t0 = a0MinusB0[0] / dirB[0];
        t1 = a1MinusB0[0] / dirB[0];
      }
      else
      {
        t0 = a0MinusB0[1] / dirB[1];
        t1 = a1MinusB0[1] / dirB[1];
      }

      if (t0 > t1) Utility.Swap(ref t0, ref t1);
      return t0 <= 1.0 && t1 >= 0.0;
    }

    /// <summary>
    /// 일반 교차 루틴(원 시그니처 유지). 공선/겹침/꼭지점 접촉 등을 구분.
    /// </summary>
    public static segmentIntersectionType Intersection(
      Segment2D s1,
      Segment2D s2,
      out Point2D i0,
      out Point2D i1,
      double domainSize)
    {
      i0 = null; i1 = null;

      double[] d1 = { s1.P1.X - s1.P0.X, s1.P1.Y - s1.P0.Y };
      double[] d2 = { s2.P1.X - s2.P0.X, s2.P1.Y - s2.P0.Y };
      double[] r  = { s1.P0.X - s2.P0.X, s1.P0.Y - s2.P0.Y };

      // 평행/비평행 판정
      double[] nd1, nd2;
      double[] nPerp;
      double perp = PerpDotOfNormalized(d1, d2, out nd1, out nd2);
      nPerp = nd2;

      if (Math.Abs(perp) < Utility.TOL9)
      {
        // 평행 -> 공선 여부 검사
        double[] rUnit = new double[2];
        double rLen = Math.Sqrt(r[0] * r[0] + r[1] * r[1]);
        if (rLen > 2.2250738585072014E-308)
        {
          rUnit[0] = r[0] / rLen; rUnit[1] = r[1] / rLen;
        }

        // r가 두 방향 벡터에 모두 수직이면 서로 다른 직선
        double crossB = nd2[0] * rUnit[1] - nd2[1] * rUnit[0];
        double crossA = nd1[0] * rUnit[1] - nd1[1] * rUnit[0];

        if (Math.Abs(crossA) > 1e-9 || Math.Abs(crossB) > 1e-9)
          return segmentIntersectionType.Disjoint;

        // 공선: s2 파라미터 공간에서 겹침 구간 추출
        if (!ComputeCollinearOverlapParams(s1, s2, d2, r, nPerp, out double t0, out double t1))
          return segmentIntersectionType.Disjoint;

        // 클램프 & 판정
        t0 = Math.Max(0.0, t0);
        t1 = Math.Min(1.0, t1);

        if (Math.Abs(t0 - t1) < 1e-9)
        {
          i0 = new Point2D(s2.P0.X + t0 * d2[0], s2.P0.Y + t0 * d2[1]);
          return segmentIntersectionType.CollinearEndPointTouch;
        }

        i0 = new Point2D(s2.P0.X + t0 * d2[0], s2.P0.Y + t0 * d2[1]);
        i1 = new Point2D(s2.P0.X + t1 * d2[0], s2.P0.Y + t1 * d2[1]);
        return segmentIntersectionType.OverlapInSegment;
      }

      // 비평행: 교차점 파라미터 계산
      double denom = d1[0] * d2[1] - d1[1] * d2[0];
      double s = (d2[0] * r[1] - d2[1] * r[0]) / denom;
      double t = (d1[0] * r[1] - d1[1] * r[0]) / denom;

      // 도메인/길이 기반 경계 근접 판단
      double len1 = Math.Sqrt(d1[0] * d1[0] + d1[1] * d1[1]);
      double len2 = Math.Sqrt(d2[0] * d2[0] + d2[1] * d2[1]);

      bool sAtStart = Math.Abs(s * len1) / domainSize < Utility.TOL6;
      bool sAtEnd   = Math.Abs((1.0 - s) * len1) / domainSize < Utility.TOL6;
      bool tAtStart = Math.Abs(t * len2) / domainSize < Utility.TOL6;
      bool tAtEnd   = Math.Abs((1.0 - t) * len2) / domainSize < Utility.TOL6;

      if (!(sAtStart || sAtEnd) && (s < -1e-9 || s > 1.000000001)) return segmentIntersectionType.Disjoint;
      if (!(tAtStart || tAtEnd) && (t < -1e-9 || t > 1.000000001)) return segmentIntersectionType.Disjoint;

      i0 = new Point2D(s1.P0.X + s * d1[0], s1.P0.Y + s * d1[1]);

      bool sStrict = !(sAtStart || sAtEnd);
      bool tStrict = !(tAtStart || tAtEnd);

      if (sStrict && tStrict) return segmentIntersectionType.Cross;          // 내부 교차
      if (sStrict ^ tStrict)  return segmentIntersectionType.Touch;          // 한쪽 끝점 접촉
      return segmentIntersectionType.EndPointTouch;                           // 양쪽 끝점 접촉
    }

    public static bool IntersectionLine(Segment2D s1, Segment2D s2, out Point2D i0)
    {
      i0 = null;

      double[] d1 = { s1.P1.X - s1.P0.X, s1.P1.Y - s1.P0.Y };
      double[] d2 = { s2.P1.X - s2.P0.X, s2.P1.Y - s2.P0.Y };
      double[] nd1, nd2;

      if (Math.Abs(PerpDotOfNormalized(d1, d2, out nd1, out nd2)) < Utility.TOL9)
        return false; // 평행

      double[] r = { s1.P0.X - s2.P0.X, s1.P0.Y - s2.P0.Y };
      double denom = d1[0] * d2[1] - d1[1] * d2[0];
      double s = (d2[0] * r[1] - d2[1] * r[0]) / denom;

      i0 = new Point2D(s1.P0.X + s * d1[0], s1.P0.Y + s * d1[1]);
      return true;
    }

    public static bool Intersection(Segment2D s1, Segment2D s2, out Point2D i0)
    {
      i0 = null;
      bool ok = IntersectionLineInternal(s1, s2, out double s, out double t, out i0);
      if (i0 == null) return ok;

      if (s > Utility.TOL9 && s < 1.0 - Utility.TOL9 &&
          t > Utility.TOL9 && t < 1.0 - Utility.TOL9)
        return true;

      i0 = null;
      return false;
    }

    public static bool IntersectionLineInternal(
      Segment2D s1, Segment2D s2,
      out double s, out double t, out Point2D i0)
    {
      s = double.MinValue; t = double.MinValue; i0 = null;

      double[] d1 = { s1.P1.X - s1.P0.X, s1.P1.Y - s1.P0.Y };
      double[] d2 = { s2.P1.X - s2.P0.X, s2.P1.Y - s2.P0.Y };
      double[] r  = { s1.P0.X - s2.P0.X, s1.P0.Y - s2.P0.Y };

      double[] nd1, nd2;
      if (Math.Abs(PerpDotOfNormalized(d1, d2, out nd1, out nd2)) < Utility.TOL9)
      {
        // 평행: 공선 여부만 반환
        double rLen = Math.Sqrt(r[0] * r[0] + r[1] * r[1]);
        if (rLen <= 2.2250738585072014E-308) return true; // 동일 시작점

        double[] rUnit = { r[0] / rLen, r[1] / rLen };
        double crossB = nd2[0] * rUnit[1] - nd2[1] * rUnit[0];
        double crossA = nd1[0] * rUnit[1] - nd1[1] * rUnit[0];
        if (Math.Abs(crossA) <= 1e-9 && Math.Abs(crossB) <= 1e-9)
        {
          // 공선
          return ComputeCollinearOverlapParams(s1, s2, d2, r, nd2, out _, out _);
        }
        return true; // 평행·이격
      }

      double denom = d1[0] * d2[1] - d1[1] * d2[0];
      s = (d2[0] * r[1] - d2[1] * r[0]) / denom;
      t = (d1[0] * r[1] - d1[1] * r[0]) / denom;

      i0 = new Point2D(s1.P0.X + s * d1[0], s1.P0.Y + s * d1[1]);
      return true;
    }

    public static bool IntersectionAndT(Segment2D s1, Segment2D s2, out Point2D i0)
    {
      i0 = null;
      bool ok = IntersectionLineInternal(s1, s2, out double s, out double t, out i0);
      if (i0 == null) return ok;

      bool sInner = s > Utility.TOL9 && s < 1.0 - Utility.TOL9;
      bool tInner = t > Utility.TOL9 && t < 1.0 - Utility.TOL9;

      if (sInner && tInner) return true;

      bool sOnEnd = (s > -Utility.TOL9 && s < 1e-9) || (s > 0.999999999 && s < 1.0 + Utility.TOL9);
      bool tOnEnd = (t > -Utility.TOL9 && t < 1e-9) || (t > 0.999999999 && t < 1.0 + Utility.TOL9);

      if (sInner && tOnEnd || sOnEnd && tInner) return true;

      i0 = null;
      return false;
    }

    public static bool AreCollinear(Segment2D s1, Segment2D s2)
    {
      var u = (Vector2D)s1; u.Normalize();
      var v = (Vector2D)s2; v.Normalize();

      if (Utility.AreEqual(0.0, Vector2D.PerpDotProduct(u, v), 1.0))
      {
        // s1.P0 -> s2.P0도 같은 직선 위인가?
        var u2 = new Vector2D(s1.P0, s2.P0);
        if (Utility.AreEqual(0.0, u2.Length, 1.0)) return true;
        u2.Normalize();
        if (Utility.AreEqual(0.0, Vector2D.PerpDotProduct(u2, v), 1.0)) return true;
      }
      return false;
    }

    public static bool AreOverlapping(Segment2D s1, Segment2D s2)
    {
      double tol = Utility.TOL9 * Math.Min(s1.Length, s2.Length);

      var u = (Vector2D)s1; u.Normalize();
      var v = (Vector2D)s2; v.Normalize();
      if (!Vector2D.AreParallel(u, v)) return false;

      // s2 파라미터로 s1의 끝점 위치를 투영
      double t0 = s2.Project(s1.P0);
      double t1 = s2.Project(s1.P1);
      if (t0 > t1) Utility.Swap(ref t0, ref t1);

      // [0,1]와 [t0,t1]의 구간 겹침 여부 (여유 tol)
      bool leftOK  = t1 >= -tol;
      bool rightOK = t0 <= 1.0 + tol;

      if (!(leftOK && rightOK)) return false;

      // 실제 겹치는 점이 존재하는지 최종 체크
      var q = s2.PointAt(Math.Clamp(0.5 * (Math.Max(0.0, t0) + Math.Min(1.0, t1)), 0.0, 1.0));
      return q.DistanceTo(s1.P0) < s1.Length + 1e-12 ||
             q.DistanceTo(s1.P1) < s1.Length + 1e-12;
    }

    #endregion

    public override string ToString()
      => string.Format($"Point2D {P0} {P1}");
  }
}
```