# Vector3D

```csharp
using System;
using System.ComponentModel;
using System.IO;

namespace Geometry
{
  [Serializable]
  public class Vector3D : Vector2D, ICloneable, IEquatable<Vector3D>
  {
    public double Z { get; set; }

    public Vector3D() { }

    public Vector3D(double x, double y) : base(x, y) { }

    public Vector3D(double x, double y, double z) : base(x, y)
    {
      Z = z;
    }

    public Vector3D(double[] v) : base(v)
    {
      if (v == null || v.Length < 3)
        throw new ArgumentException("Vector3D(double[]) requires length >= 3");
      Z = v[2];
    }

    public Vector3D(Point3D from, Point3D to)
    {
      X = to.X - from.X;
      Y = to.Y - from.Y;
      Z = to.Z - from.Z;
    }

    /// <summary>세 점으로 평면 법선을 추정(가장 견고한 축 선택)</summary>
    public Vector3D(Point3D p0, Point3D p1, Point3D p2)
    {
      Zero();

      var e01 = Subtract(p1, p0);
      var e12 = Subtract(p2, p1);
      var e20 = Subtract(p0, p2);

      var n1 = Cross(e20, e01); if (!n1.Normalize()) return;
      var n2 = Cross(e01, e12); if (!n2.Normalize()) return;
      var n3 = Cross(e12, e20); if (!n3.Normalize()) return;

      double inv01 = 1.0 / Math.Max(e01.Length, 1e-30);
      double inv12 = 1.0 / Math.Max(e12.Length, 1e-30);
      double inv20 = 1.0 / Math.Max(e20.Length, 1e-30);

      // 각 후보 법선에 대한 “안정성” 점수 계산
      double s1 = inv01 * Math.Abs(Dot(n1, e01)) + inv12 * Math.Abs(Dot(n1, e12)) + inv20 * Math.Abs(Dot(n1, e20));
      double s2 = inv01 * Math.Abs(Dot(n2, e01)) + inv12 * Math.Abs(Dot(n2, e12)) + inv20 * Math.Abs(Dot(n2, e20));
      double s3 = inv01 * Math.Abs(Dot(n3, e01)) + inv12 * Math.Abs(Dot(n3, e12)) + inv20 * Math.Abs(Dot(n3, e20));

      var chosen = (s1 <= s2 && s1 <= s3) ? n1 : (s2 <= s3 ? n2 : n3);
      X = chosen.X; Y = chosen.Y; Z = chosen.Z;
    }

    protected Vector3D(Vector3D other) : base(other) { Z = other.Z; }

    public override double[] ToArray() => new[] { X, Y, Z };

    public override bool IsValid() => base.IsValid() && !double.IsNaN(Z);

    public override object Clone() => new Vector3D(this);

    public double this[int index]
    {
      get => index switch { 0 => X, 1 => Y, 2 => Z, _ => throw new IndexOutOfRangeException() };
      set
      {
        switch (index)
        {
          case 0: X = value; break;
          case 1: Y = value; break;
          case 2: Z = value; break;
          default: throw new IndexOutOfRangeException();
        }
      }
    }

    public static Vector3D AxisX      => new Vector3D( 1,  0,  0);
    public static Vector3D AxisMinusX => new Vector3D(-1,  0,  0);
    public static Vector3D AxisY      => new Vector3D( 0,  1,  0);
    public static Vector3D AxisMinusY => new Vector3D( 0, -1,  0);
    public static Vector3D AxisZ      => new Vector3D( 0,  0,  1);
    public static Vector3D AxisMinusZ => new Vector3D( 0,  0, -1);

    public new bool IsZero => Length < 1e-9;

    public new bool IsUnit => Math.Abs(1.0 - Length) < 1e-9;

    public new double Length
    {
      get => Math.Sqrt(X * X + Y * Y + Z * Z);
      set
      {
        double len = Length;
        if (len <= 1e-9) return;
        double s = value / len;
        X *= s; Y *= s; Z *= s;
      }
    }

    public new double LengthSquared => X * X + Y * Y + Z * Z;

    /// <summary>
    /// v에 수직인 임의의 벡터를 설정. v=0이면 false.
    /// </summary>
    public bool PerpendicularTo(Vector3D v)
    {
      double ax = Math.Abs(v.X), ay = Math.Abs(v.Y), az = Math.Abs(v.Z);
      if (ax + ay + az <= 0) { Zero(); return false; }

      // 가장 작은 성분을 0으로 두고 나머지 두 성분으로 수직 벡터 구성
      if (ax <= ay && ax <= az) { X = 0;     Y = -v.Z; Z =  v.Y; }
      else if (ay <= ax && ay <= az) { X =  v.Z; Y = 0;     Z = -v.X; }
      else { X = -v.Y; Y =  v.X; Z = 0; }
      return true;
    }

    public new void Zero() { X = 0; Y = 0; Z = 0; }

    // -------- 연산자 --------
    public static Vector3D operator -(Vector3D a, Vector3D b) => new Vector3D(a.X - b.X, a.Y - b.Y, a.Z - b.Z);
    public static Vector3D operator +(Vector3D a, Vector3D b) => new Vector3D(a.X + b.X, a.Y + b.Y, a.Z + b.Z);
    public static Vector3D operator *(double s, Vector3D v)   => new Vector3D(s * v.X, s * v.Y, s * v.Z);
    public static Vector3D operator *(Vector3D v, double s)   => new Vector3D(s * v.X, s * v.Y, s * v.Z);
    public static Vector3D operator /(Vector3D v, double s)
    {
      if (Math.Abs(s) < 1e-30) throw new DivideByZeroException();
      return new Vector3D(v.X / s, v.Y / s, v.Z / s);
    }

    public static bool operator ==(Vector3D left, Vector3D right) => Equals(left, right);
    public static bool operator !=(Vector3D left, Vector3D right) => !Equals(left, right);

    public new bool Normalize()
    {
      double len = Length;
      if (len <= 2.2250738585072014E-308) return false; // ~Double.MinNormal
      X /= len; Y /= len; Z /= len;
      return true;
    }

    public double AngleInXY  => Math.Atan2(Y, X);
    public double AngleFromXY => Math.Atan2(Z, Math.Sqrt(X * X + Y * Y));

    public Point3D AsPoint => new Point3D(X, Y, Z);

    public static double Dot(Vector3D u, Vector3D v) => u.X * v.X + u.Y * v.Y + u.Z * v.Z;
    public static double operator *(Vector3D u, Vector3D v) => Dot(u, v);

    public static double Dot(Vector3D v, Point3D p) => v.X * p.X + v.Y * p.Y + v.Z * p.Z;
    public static double Dot(Point3D p, Vector3D v) => p.X * v.X + p.Y * v.Y + p.Z * v.Z;

    public static Vector3D Subtract(Point3D a, Point3D b) => new Vector3D(a.X - b.X, a.Y - b.Y, a.Z - b.Z);
    public static Vector3D Add(Point3D a, Point3D b)      => new Vector3D(a.X + b.X, a.Y + b.Y, a.Z + b.Z);

    public static Vector3D Cross(Vector3D a, Vector3D b)
      => new Vector3D(a.Y * b.Z - a.Z * b.Y, a.Z * b.X - a.X * b.Z, a.X * b.Y - a.Y * b.X);

    public static Vector3D Cross(Point3D a, Point3D b)
      => new Vector3D(a.Y * b.Z - a.Z * b.Y, a.Z * b.X - a.X * b.Z, a.X * b.Y - a.Y * b.X);

    public void WriteAsFloat(BinaryWriter bw)
    {
      bw.Write((float)X);
      bw.Write((float)Y);
      bw.Write((float)Z);
    }

    /// <summary>정규화된 내적 기반 각도(라디안). 제로 벡터면 0 반환.</summary>
    public static double AngleBetween(Vector3D u, Vector3D v)
    {
      double lu = u.Length, lv = v.Length;
      if (lu <= 0 || lv <= 0) return 0.0;
      double cos = Dot(u, v) / (lu * lv);
      Utility.LimitRange(-1.0, ref cos, 1.0);
      return Math.Acos(cos);
    }

    // 동등성
    public bool Equals(Vector3D other)
    {
      if (ReferenceEquals(other, null)) return false;
      return base.Equals((Vector2D)other) && Utility.Compare(other.Z, Z) == 0;
    }

    public override bool Equals(object obj) => obj is Vector3D v && Equals(v);

    public override int GetHashCode() => base.GetHashCode() * 397 ^ Z.GetHashCode();

    public override string ToString() => $"Vector3D: X={X}, Y={Y}, Z={Z}, Length={Length}";

    public new virtual void Negate() { X = -X; Y = -Y; Z = -Z; }

    // 관계 판정 (정규화 기반; 제로 벡터 방지)
    public static bool AreCoincident(Vector3D u, Vector3D v) => AreCoincident(u, v, Utility.TOL9);
    public static bool AreCoincident(Vector3D u, Vector3D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(1.0 - cos) < tol;
    }

    public static bool AreOpposite(Vector3D u, Vector3D v) => AreOpposite(u, v, Utility.TOL9);
    public static bool AreOpposite(Vector3D u, Vector3D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(cos + 1.0) < tol;
    }

    public static bool AreParallel(Vector3D u, Vector3D v) => AreParallel(u, v, Utility.TOL9);
    public static bool AreParallel(Vector3D u, Vector3D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(Math.Abs(cos) - 1.0) < tol;
    }

    public static bool AreOrthogonal(Vector3D u, Vector3D v) => AreOrthogonal(u, v, Utility.TOL9);
    public static bool AreOrthogonal(Vector3D u, Vector3D v, double tol)
    {
      if (u.IsZero || v.IsZero) return false;
      double cos = Dot(u, v) / (u.Length * v.Length);
      return Math.Abs(cos) < tol;
    }

    public override void TransformBy(Transformation xform)
    {
      double[] h = xform.ActOnLeft(X, Y, Z, 0.0); // (x,y,z,w)
      double invW = h[3] != 0.0 ? 1.0 / h[3] : 1.0;
      X = invW * h[0];
      Y = invW * h[1];
      Z = invW * h[2];
    }

    public override double MaximumCoordinate
    {
      get
      {
        double max = base.MaximumCoordinate;
        double az = Math.Abs(Z);
        return az > max ? az : max;
      }
    }

    // 편의 분해
    public void Deconstruct(out double x, out double y, out double z)
    {
      x = X; y = Y; z = Z;
    }
  }
}


```
