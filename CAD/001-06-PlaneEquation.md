# PlaneEquation
```csharp

using System;
using System.Diagnostics;
using System.Globalization;

namespace Geometry
{
  /// <summary>
  /// 평면 방정식 A·x + B·y + C·z + D = 0
  /// (A,B,C)는 법선 벡터 성분이며, Vector3D(X= A, Y= B, Z= C)를 상속합니다.
  /// </summary>
  [Serializable]
  [DebuggerDisplay("Plane: {A}x + {B}y + {C}z + {D} = 0")]
  public class PlaneEquation : Vector3D
  {
    private const double Epsilon = 1e-12;

    /// <summary>D 항 (오프셋)</summary>
    public double D;

    /// <summary>A = X</summary>
    public double A { get => X; set => X = value; }
    /// <summary>B = Y</summary>
    public double B { get => Y; set => Y = value; }
    /// <summary>C = Z</summary>
    public double C { get => Z; set => Z = value; }

    public PlaneEquation() { }

    /// <summary>점과 법선으로 평면 생성(법선 자동 정규화). 유효하지 않으면 예외.</summary>
    public PlaneEquation(Point3D point, Vector3D normal)
    {
      if (!Create(point, normal))
        throw new ArgumentException("Invalid plane equation: point/normal are not valid or normal is near zero.");
    }

    /// <summary>A,B,C,D로 생성. 필요 시 정규화합니다.</summary>
    public PlaneEquation(double a, double b, double c, double d)
    {
      A = a; B = b; C = c; D = d;
      NormalizeEquation(); // (A,B,C,D) -> |N|=1 이 되도록 정규화 (가능한 경우)
    }

    protected PlaneEquation(PlaneEquation other) : base(other)
    {
      D = other.D;
    }

    public override object Clone() => new PlaneEquation(this);

    /// <summary>계수 부호 반전: (A,B,C,D) -> -(A,B,C,D)</summary>
    public override void Negate()
    {
      base.Negate();
      D = -D;
    }

    /// <summary>
    /// 점과 법선으로 평면 정의. 법선이 0에 가깝다면 false.
    /// 성공 시 (A,B,C)는 단위 법선, D는 -N·P 가 됩니다.
    /// </summary>
    public bool Create(Point3D point, Vector3D normal)
    {
      if (point == null || normal == null || !point.IsValid() || !normal.IsValid())
        return false;

      // 법선 복사 후 정규화
      A = normal.X; B = normal.Y; C = normal.Z;
      if (!Normalize()) // Vector3D.Normalize(): 길이 0 근처면 false
        return false;

      D = -(A * point.X + B * point.Y + C * point.Z);
      return true;
    }

    /// <summary>세 점으로 평면 생성. 세 점이 일직선/중복이면 false.</summary>
    public static bool FromPoints(Point3D p, Point3D q, Point3D r, out PlaneEquation plane)
    {
      plane = null;
      if (p == null || q == null || r == null) return false;

      var n = Vector3D.Cross(Vector3D.Subtract(q, p), Vector3D.Subtract(r, p));
      if (!n.Normalize()) return false;

      var pe = new PlaneEquation();
      if (!pe.Create(p, n)) return false;

      plane = pe;
      return true;
    }

    /// <summary>점에서 평면까지의 부호 있는 거리. (법선이 단위가 아니면 자동 보정)</summary>
    public double SignedDistance(Point3D point)
    {
      if (point == null) return double.NaN;
      double denom = Length; // |N|
      if (denom <= Epsilon) return double.NaN; // 잘못된 평면
      return (A * point.X + B * point.Y + C * point.Z + D) / denom;
    }

    /// <summary>평면 방정식 값 f(P)=A·x+B·y+C·z+D (정규화 여부 무관)</summary>
    public double ValueAt(Point3D p) => A * p.X + B * p.Y + C * p.Z + D;

    /// <summary>평면 방정식 값 f(x,y,z)=A·x+B·y+C·z+D</summary>
    public double ValueAt(double x, double y, double z) => A * x + B * y + C * z + D;

    /// <summary>
    /// (A,B,C,D)를 정규화. |N|≈0이면 아무 것도 하지 않고 false 반환.
    /// 성공 시 |N|=1 이고, D도 동일 비율로 스케일됩니다.
    /// </summary>
    public bool NormalizeEquation()
    {
      double len = Length; // |(A,B,C)|
      if (len <= Epsilon) return false;

      if (Math.Abs(1.0 - len) <= Epsilon) return true; // 이미 단위

      // Vector3D.Normalize()는 A,B,C를 1/len으로 만들므로 D도 동일 비율로 보정
      Normalize();
      D /= len;
      return true;
    }

    /// <summary>유효성: 계수들이 유효 숫자이며 법선이 NaN이 아니고 D도 NaN이 아님.</summary>
    public override bool IsValid()
    {
      if (!base.IsValid() || double.IsNaN(D)) return false;
      // 완전 영벡터는 방정식으로서 의미가 없음
      return Length > Epsilon;
    }

    /// <summary>"A x + B y + C z + D = 0" 형태 문자열</summary>
    public override string ToString()
    {
      // InvariantCulture로 고정(로깅/디버그에 적합)
      return string.Create(CultureInfo.InvariantCulture,
        $"{A}x + {B}y + {C}z + {D} = 0");
    }

    public override double[] ToArray() => new[] { A, B, C, D };
  }
}


```

