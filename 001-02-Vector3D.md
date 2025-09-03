# Vector3D

```csharp
namespace Geometry
{
  [Serializable]
  public class Vector3D : Vector2D
  {
    public double Z;

    public Vector3D()
    {
    }

    public Vector3D(double x, double y)
      : base(x, y)
    {
    }

    public Vector3D(double x, double y, double z)
      : base(x, y)
    {
      this.Z = z;
    }

    public Vector3D(double[] v)
      : base(v)
    {
      this.Z = v[2];
    }

    public Vector3D(Point3D P0, Point3D P1)
    {
      this.X = P1.X - P0.X;
      this.Y = P1.Y - P0.Y;
      this.Z = P1.Z - P0.Z;
    }

    public Vector3D(Point3D P0, Point3D P1, Point3D P2)
    {
      this.Zero();
      Vector3D vector3D1 = Vector3D.Subtract(P2, P1);
      Vector3D vector3D2 = Vector3D.Subtract(P0, P2);
      Vector3D vector3D3 = Vector3D.Subtract(P1, P0);
      Vector3D u1 = Vector3D.Cross(vector3D2, vector3D3);
      if (!u1.Normalize())
        return;
      Vector3D u2 = Vector3D.Cross(vector3D3, vector3D1);
      if (!u2.Normalize())
        return;
      Vector3D u3 = Vector3D.Cross(vector3D1, vector3D2);
      if (!u3.Normalize())
        return;
      double num1 = 1.0 / vector3D1.Length;
      double num2 = 1.0 / vector3D2.Length;
      double num3 = 1.0 / vector3D3.Length;
      double num4 = num1 * Math.Abs(Vector3D.Dot(u1, vector3D1)) 
        + num2 * Math.Abs(Vector3D.Dot(u1, vector3D2)) 
        + num3 * Math.Abs(Vector3D.Dot(u1, vector3D3));
      double num5 = num1 * Math.Abs(Vector3D.Dot(u2, vector3D1)) 
        + num2 * Math.Abs(Vector3D.Dot(u2, vector3D2)) 
        + num3 * Math.Abs(Vector3D.Dot(u2, vector3D3));
      double num6 = num1 * Math.Abs(Vector3D.Dot(u3, vector3D1)) 
        + num2 * Math.Abs(Vector3D.Dot(u3, vector3D2)) 
        + num3 * Math.Abs(Vector3D.Dot(u3, vector3D3));
      if (num4 <= num5)
      {
        if (num4 <= num6)
        {
          this.X = u1.X;
          this.Y = u1.Y;
          this.Z = u1.Z;
        }
        else
        {
          this.X = u3.X;
          this.Y = u3.Y;
          this.Z = u3.Z;
        }
      }
      else if (num5 <= num6)
      {
        this.X = u2.X;
        this.Y = u2.Y;
        this.Z = u2.Z;
      }
      else
      {
        this.X = u3.X;
        this.Y = u3.Y;
        this.Z = u3.Z;
      }
    }

    protected Vector3D(Vector3D another)
      : base((Vector2D) another)
    {
      this.Z = another.Z;
    }

    public override double[] ToArray()
    {
      return new double[3]{ this.X, this.Y, this.Z };
    }

    public override bool IsValid() => base.IsValid() && !double.IsNaN(this.Z);

    public override object Clone() => (object) new Vector3D(this);

    public double this[int index]
    {
      get
      {
        if (index == 0)
          return this.X;
        return index == 1 ? this.Y : this.Z;
      }
    }

    public static Vector3D AxisX => new Vector3D(1.0, 0.0, 0.0);

    public static Vector3D AxisMinusX => new Vector3D(-1.0, 0.0, 0.0);

    public static Vector3D AxisY => new Vector3D(0.0, 1.0, 0.0);

    public static Vector3D AxisMinusY => new Vector3D(0.0, -1.0, 0.0);

    public static Vector3D AxisZ => new Vector3D(0.0, 0.0, 1.0);

    public static Vector3D AxisMinusZ => new Vector3D(0.0, 0.0, -1.0);

    [Browsable(false)]
    public new bool IsZero => this.Length < 1E-09;

    [Browsable(false)]
    public new bool IsUnit => Math.Abs(1.0 - this.Length) < 1E-09;

    [Browsable(false)]
    public new double Length
    {
      get => Math.Sqrt(this.X * this.X + this.Y * this.Y + this.Z * this.Z);
      set
      {
        double num = Math.Sqrt(this.X * this.X + this.Y * this.Y + this.Z * this.Z);
        if (num <= 1E-09)
          return;
        this.X *= value / num;
        this.Y *= value / num;
        this.Z *= value / num;
      }
    }

    public new double LengthSquared => this.X * this.X + this.Y * this.Y + this.Z * this.Z;

    public bool PerpendicularTo(Vector3D v)
    {
      int index1;
      int index2;
      int index3;
      double num1;
      double num2;
      if (Math.Abs(v.Y) > Math.Abs(v.X))
      {
        if (Math.Abs(v.Z) > Math.Abs(v.Y))
        {
          index1 = 2;
          index2 = 1;
          index3 = 0;
          num1 = v.Z;
          num2 = -v.Y;
        }
        else if (Math.Abs(v.Z) >= Math.Abs(v.X))
        {
          index1 = 1;
          index2 = 2;
          index3 = 0;
          num1 = v.Y;
          num2 = -v.Z;
        }
        else
        {
          index1 = 1;
          index2 = 0;
          index3 = 2;
          num1 = v.Y;
          num2 = -v.X;
        }
      }
      else if (Math.Abs(v.Z) > Math.Abs(v.X))
      {
        index1 = 2;
        index2 = 0;
        index3 = 1;
        num1 = v.Z;
        num2 = -v.X;
      }
      else if (Math.Abs(v.Z) > Math.Abs(v.Y))
      {
        index1 = 0;
        index2 = 2;
        index3 = 1;
        num1 = v.X;
        num2 = -v.Z;
      }
      else
      {
        index1 = 0;
        index2 = 1;
        index3 = 2;
        num1 = v.X;
        num2 = -v.Y;
      }
      double[] numArray = new double[3];
      numArray[index1] = num2;
      numArray[index2] = num1;
      numArray[index3] = 0.0;
      this.X = numArray[0];
      this.Y = numArray[1];
      this.Z = numArray[2];
      return num1 != 0.0;
    }

    public new void Zero() => this.X = this.Y = this.Z = 0.0;

    public static Vector3D operator -(Vector3D v1, Vector3D v2)
    {
      return new Vector3D(v1.X - v2.X, v1.Y - v2.Y, v1.Z - v2.Z);
    }

    public static Vector3D operator +(Vector3D a, Vector3D b)
    {
      return new Vector3D(a.X + b.X, a.Y + b.Y, a.Z + b.Z);
    }

    public static Vector3D operator *(double s, Vector3D v)
    {
      return new Vector3D(s * v.X, s * v.Y, s * v.Z);
    }

    public static Vector3D operator *(Vector3D v, double s)
    {
      return new Vector3D(s * v.X, s * v.Y, s * v.Z);
    }

    public static Vector3D operator /(Vector3D v, double s)
    {
      return new Vector3D(v.X / s, v.Y / s, v.Z / s);
    }

    public static bool operator ==(Vector3D left, Vector3D right)
    {
      return object.Equals((object) left, (object) right);
    }

    public static bool operator !=(Vector3D left, Vector3D right)
    {
      return !object.Equals((object) left, (object) right);
    }

    public new bool Normalize()
    {
      double num = Math.Sqrt(this.X * this.X + this.Y * this.Y + this.Z * this.Z);
      if (num <= 2.2250738585072014E-308)
        return false;
      this.X /= num;
      this.Y /= num;
      this.Z /= num;
      return true;
    }

    [Browsable(false)]
    public double AngleInXY => Math.Atan2(this.Y, this.X);

    [Browsable(false)]
    public double AngleFromXY => Math.Atan2(this.Z, Math.Sqrt(this.X * this.X + this.Y * this.Y));

    [Browsable(false)]
    public Point3D AsPoint => new Point3D(this.X, this.Y, this.Z);

    public static double Dot(Vector3D u, Vector3D v) => u.X * v.X + u.Y * v.Y + u.Z * v.Z;

    public static double operator *(Vector3D u, Vector3D v) => u.X * v.X + u.Y * v.Y + u.Z * v.Z;

    public static double Dot(Vector3D v, Point3D p) => v.X * p.X + v.Y * p.Y + v.Z * p.Z;

    public static double Dot(Point3D p, Vector3D v) => p.X * v.X + p.Y * v.Y + p.Z * v.Z;

    public static Vector3D Subtract(Point3D a, Point3D b)
    {
      return new Vector3D(a.X - b.X, a.Y - b.Y, a.Z - b.Z);
    }

    public static Vector3D Add(Point3D a, Point3D b)
    {
      return new Vector3D(a.X + b.X, a.Y + b.Y, a.Z + b.Z);
    }

    public static Vector3D Cross(Vector3D a, Vector3D b)
    {
      return new Vector3D(a.Y * b.Z - a.Z * b.Y, a.Z * b.X - a.X * b.Z, a.X * b.Y - a.Y * b.X);
    }

    public static Vector3D Cross(Point3D a, Point3D b)
    {
      return new Vector3D(a.Y * b.Z - a.Z * b.Y, a.Z * b.X - a.X * b.Z, a.X * b.Y - a.Y * b.X);
    }

    public void WriteAsFloat(BinaryWriter bw)
    {
      bw.Write((float) this.X);
      bw.Write((float) this.Y);
      bw.Write((float) this.Z);
    }

    public static double AngleBetween(Vector3D u, Vector3D v)
    {
      double d = u * v;
      Utility.LimitRange<double>(-1.0, ref d, 1.0);
      return Math.Acos(d);
    }

    public bool Equals(Vector3D other)
    {
      return this.Equals((Vector2D) other) && Utility.Compare(other.Z, this.Z) == 0;
    }

    public override bool Equals(object obj)
    {
      return (object) (obj as Vector3D) != null && this.Equals((Vector3D) obj);
    }

    public override int GetHashCode() => base.GetHashCode() * 397 ^ this.Z.GetHashCode();

    public override string ToString()
    {
      return string.Format("{0}, {1}, {2} : {3}", (object) this.X, (object) this.Y, (object) this.Z, (object) this.Length);
    }

    public new virtual void Negate()
    {
      this.X = -this.X;
      this.Y = -this.Y;
      this.Z = -this.Z;
    }

    public static bool AreCoincident(Vector3D u, Vector3D v)
    {
      return Vector3D.AreCoincident(u, v, Utility.TOL9);
    }

    public static bool AreCoincident(Vector3D u, Vector3D v, double tol)
    {
      return Math.Abs(1.0 - u * v) < tol;
    }

    public static bool AreOpposite(Vector3D u, Vector3D v)
    {
      return Vector3D.AreOpposite(u, v, Utility.TOL9);
    }

    public static bool AreOpposite(Vector3D u, Vector3D v, double tol)
    {
      return Math.Abs(u * v + 1.0) < tol;
    }

    public static bool AreParallel(Vector3D u, Vector3D v)
    {
      return Vector3D.AreParallel(u, v, Utility.TOL9);
    }

    public static bool AreParallel(Vector3D u, Vector3D v, double tol)
    {
      return Math.Abs(Math.Abs(u * v) - 1.0) < tol;
    }

    public static bool AreOrthogonal(Vector3D u, Vector3D v)
    {
      return Vector3D.AreOrthogonal(u, v, Utility.TOL9);
    }

    public static bool AreOrthogonal(Vector3D u, Vector3D v, double tol) => Math.Abs(u * v) < tol;

    public override void TransformBy(Transformation xform)
    {
      double[] numArray = xform.ActOnLeft(this.X, this.Y, this.Z, 0.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      this.X = num * numArray[0];
      this.Y = num * numArray[1];
      this.Z = num * numArray[2];
    }

    public override double MaximumCoordinate
    {
      get
      {
        double maximumCoordinate = base.MaximumCoordinate;
        if (Math.Abs(this.Z) > maximumCoordinate)
          maximumCoordinate = Math.Abs(this.Z);
        return maximumCoordinate;
      }
    }
  }
}
```