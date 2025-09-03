# PlaneEquation
```csharp

namespace Geometry
{
  [Serializable]
  public class PlaneEquation : Vector3D
  {
    public double D;

    public PlaneEquation()
    {
    }

    public PlaneEquation(Point3D P, Vector3D N)
    {
      if (!this.Create(P, N))
        throw new ArgumentException("Invalid Plane Equation");
    }

    public PlaneEquation(double a, double b, double c, double d)
    {
      this.X = a;
      this.Y = b;
      this.Z = c;
      this.D = d;
      double length = this.Length;
      if (Math.Abs(1.0 - length) <= 1E-12)
        return;
      this.Normalize();
      this.D = d / length;
    }

    protected PlaneEquation(PlaneEquation another)
      : base((Vector3D) another)
    {
      this.D = another.D;
    }

    public override void Negate()
    {
      base.Negate();
      this.D = -this.D;
    }

    public bool Create(Point3D P, Vector3D N)
    {
      bool flag = false;
      if (P.IsValid() && N.IsValid())
      {
        this.X = N.X;
        this.Y = N.Y;
        this.Z = N.Z;
        flag = Math.Abs(1.0 - this.Length) <= 1E-12 || this.Normalize();
        this.D = -(this.X * P.X + this.Y * P.Y + this.Z * P.Z);
      }
      return flag;
    }

    public override object Clone() => (object) new PlaneEquation(this);

    public double ValueAt(Point3D P) => this.X * P.X + this.Y * P.Y + this.Z * P.Z + this.D;

    public double ValueAt(double x, double y, double z)
    {
      return this.X * x + this.Y * y + this.Z * z + this.D;
    }

    public override bool IsValid() => base.IsValid() && !double.IsNaN(this.D);

    public override string ToString()
    {
      return string.Format(ErrMessage.GetString(425481926), (object) this.X, (object) this.Y, (object) this.Z, (object) this.D);
    }

    public override double[] ToArray()
    {
      return new double[4]{ this.X, this.Y, this.Z, this.D };
    }
  }
}

```
