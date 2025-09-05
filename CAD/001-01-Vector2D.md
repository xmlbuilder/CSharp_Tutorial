## Vector2D
```csharp
using System;

namespace Geometry
{
  [Serializable]
  public class Vector2D : ICloneable, IEquatable<Vector2D>
  {
    // 공개 API 유지: X, Y는 여전히 필드처럼 접근 가능 (프로퍼티로 변경)
    public double X { get; set; }
    public double Y { get; set; }

    public Vector2D() { }

    public Vector2D(double x, double y)
    {
      X = x;
      Y = y;
    }

    public Vector2D(Point2D from, Point2D to)
    {
      X = to.X - from.X;
      Y = to.Y - from.Y;
    }

    public Vector2D(double[] v)
    {
      if (v == null || v.Length < 2)
        throw new ArgumentException("Vector2D(double[]) requires length >= 2");
      X = v[0];
      Y = v[1];
    }

    protected Vector2D(Vector2D other)
    {
      X = other.X;
      Y = other.Y;
    }

    public virtual object Clone() => new Vector2D(this);

    public virtual double[] ToArray() => new[] { X, Y };

    public virtual bool IsValid() => !double.IsNaN(X) && !double.IsNaN(Y);

    public virtual double MaximumCoordinate
    {
      get
      {
        double ax = Math.Abs(X);
        double ay = Math.Abs(Y);
        return ax >= ay ? ax : ay;
      }
    }

    // ---------- 연산자 ----------
    public static Vector2D operator *(double s, Vector2D v) => new Vector2D(s * v.X, s * v.Y);
    public static Vector2D operator *(Vector2D v, double s) => new Vector2D(s * v.X, s * v.Y);
    public static Vector2D operator -(Vector2D u, Vector2D v) => new Vector2D(u.X - v.X, u.Y - v.Y);
    public static Vector2D operator +(Vector2D u, Vector2D v) => new Vector2D(u.X + v.X, u.Y + v.Y);
    public static double   operator *(Vector2D u, Vector2D v) => Dot(u, v);

    public static bool operator ==(Vector2D left, Vector2D right)  => Equals(left, right);
    public static bool operator !=(Vector2D left, Vector2D right)  => !Equals(left, right);

    // ---------- 정적 유틸 ----------
    public static Vector2D Subtract(Point2D a, Point2D b) => new Vector2D(a.X - b.X, a.Y - b.Y);
    public static Vector2D Add(Point2D a, Point2D b)      => new Vector2D(a.X + b.X, a.Y + b.Y);

    public static double Dot(Vector2D u, Vector2D v) => u.X * v.X + u.Y * v.Y;
    public static double Dot(Vector2D v, Point2D p)  => v.X * p.X + v.Y * p.Y;
    public static double Dot(Point2D p, Vector2D v)  => p.X * v.X + p.Y * v.Y;

    public static double PerpDotProduct(Vector2D u, Vector2D v) => u.X * v.Y - u.Y * v.X;

    // 길이 & 정규화
    public bool Normalize()
    {
      double len = Length;
      if (len <= 2.2250738585072014E-308) // ~Double.MinNormal
        return false;
      X /= len;
      Y /= len;
      return true;
    }

    public void Negate()
    {
      X = -X;
      Y = -Y;
    }

    public void Reverse() => Negate();

    public Point2D AsPoint => new Point2D(X, Y);

    public bool IsUnit => Math.Abs(1.0 - Length) < 1E-09;
    public bool IsZero => Length < 1E-09;

    public double Length
    {
      get => Math.Sqrt(X * X + Y * Y);
      set
      {
        double len = Length;
        if (len <= 1E-09) return;
        double s = value / len;
        X *= s;
        Y *= s;
      }
    }

    public double LengthSquared => X * X + Y * Y;

    public static Vector2D AxisX => new Vector2D(1.0, 0.0);
    public static Vector2D AxisY => new Vector2D(0.0, 1.0);

    // 각도
    public double Angle => Math.Atan2(Y, X);

    /// <summary>
    /// 두 벡터 사이 각도(라디안). 스케일에 영향을 받지 않도록 정규화하여 계산.
    /// </summary>
    public static double AngleBetween(Vector2D u, Vector2D v)
    {
      double nu = u.Length;
      double nv = v.Length;
      if (nu <= 0 || nv <= 0) return 0.0;

      double cos = Dot(u, v) / (nu * nv);
      Utility.LimitRange(-1.0, ref cos, 1.0); // [-1,1] clamp
      return Math.Acos(cos);
    }

    /// <summary>
    /// 부호 있는 각도(라디안). atan2(외적(2D), 내적) 형태라 스케일에 무관.
    /// </summary>
    public static double SignedAngleBetween(Vector2D u, Vector2D v)
    {
      return Math.Atan2(PerpDotProduct(u, v), Dot(u, v));
    }

    // 관계 판정 (정규화된 내적 기반; 영벡터 방지)
    public static bool AreCoincident(Vector2D u, Vector2D v) => AreCoincident(u, v, Utility.TOL9);
    public static bool AreCoincident(Vector2D u, Vector2D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(1.0 - cos) < tol;
    }

    public static bool AreOpposite(Vector2D u, Vector2D v) => AreOpposite(u, v, Utility.TOL9);
    public static bool AreOpposite(Vector2D u, Vector2D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(cos + 1.0) < tol;
    }

    public static bool AreParallel(Vector2D u, Vector2D v) => AreParallel(u, v, Utility.TOL9);
    public static bool AreParallel(Vector2D u, Vector2D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(Math.Abs(cos) - 1.0) < tol;
    }

    public static bool AreOrthogonal(Vector2D u, Vector2D v) => AreOrthogonal(u, v, Utility.TOL9);
    public static bool AreOrthogonal(Vector2D u, Vector2D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      // 내적을 길이로 정규화해 스케일 무관
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(cos) < tol;
    }

    // 동등성
    public bool Equals(Vector2D other)
    {
      if (ReferenceEquals(other, null)) return false;
      return Utility.Compare(other.X, X) == 0 && Utility.Compare(other.Y, Y) == 0;
    }

    public override bool Equals(object obj) => obj is Vector2D v && Equals(v);

    public override int GetHashCode() => X.GetHashCode() * 397 ^ Y.GetHashCode();

    public override string ToString()
    {
      return $"Vector2D: X={X}, Y={Y}, Length={Length}";
    }

    // 선형변환 (동차좌표 행렬 가정)
    public virtual void TransformBy(Transformation xform)
    {
      double[] h = xform.ActOnLeft(X, Y, 0.0, 0.0); // (x, y, z=0, w)
      double invW = h[3] != 0.0 ? 1.0 / h[3] : 1.0;
      X = invW * h[0];
      Y = invW * h[1];
    }

    // 편의: 분해
    public void Deconstruct(out double x, out double y)
    {
      x = X; y = Y;
    }
  }
}

```

## ✨ 샘플 활용 예제: 두 점 사이의 단위 벡터 계산
```csharp
Point2D A = new Point2D(0, 0);
Point2D B = new Point2D(3, 4);
Vector2D direction = new Vector2D(A, B);
direction.Normalize();

Console.WriteLine($"단위 벡터: ({direction.X}, {direction.Y})");
Console.WriteLine($"길이: {direction.Length}"); // 출력: 1.0
```
---







