## Vector2D
```csharp
namespace Geometry
{
  [Serializable]
  public class Vector2D : ICloneable
  {
    public double X;
    public double Y;

    public Vector2D()
    {
    }

    public Vector2D(double x, double y)
    {
      this.X = x;
      this.Y = y;
    }

    public Vector2D(Point2D P0, Point2D P1)
    {
      this.X = P1.X - P0.X;
      this.Y = P1.Y - P0.Y;
    }

    public Vector2D(double[] v)
    {
      this.X = v[0];
      this.Y = v[1];
    }

    protected Vector2D(Vector2D another)
    {
      this.X = another.X;
      this.Y = another.Y;
    }

    public virtual object Clone() => (object) new Vector2D(this);

    public virtual double[] ToArray()
    {
      return new double[2]{ this.X, this.Y };
    }

    public virtual bool IsValid() => !double.IsNaN(this.X) && !double.IsNaN(this.Y);


    public virtual double MaximumCoordinate
    {
      get
      {
        double maximumCoordinate = Math.Abs(this.X);
        if (Math.Abs(this.Y) > maximumCoordinate)
          maximumCoordinate = Math.Abs(this.Y);
        return maximumCoordinate;
      }
    }

    public static Vector2D operator *(double s, Vector2D v) => new Vector2D(s * v.X, s * v.Y);

    public static Vector2D operator *(Vector2D v, double s) => new Vector2D(s * v.X, s * v.Y);

    public static Vector2D Subtract(Point2D a, Point2D b) => new Vector2D(a.X - b.X, a.Y - b.Y);

    public static Vector2D Add(Point2D a, Point2D b) => new Vector2D(a.X + b.X, a.Y + b.Y);

    public bool Normalize()
    {
      double num = Math.Sqrt(this.X * this.X + this.Y * this.Y);
      if (num <= 2.2250738585072014E-308)
        return false;
      this.X /= num;
      this.Y /= num;
      return true;
    }

    public void Reverse()
    {
      this.X = -this.X;
      this.Y = -this.Y;
    }


    public Point2D AsPoint => new Point2D(this.X, this.Y);


    public bool IsUnit => Math.Abs(1.0 - this.Length) < 1E-09;


    public bool IsZero => this.Length < 1E-09;


    public double Length
    {
      get => Math.Sqrt(this.X * this.X + this.Y * this.Y);
      set
      {
        double num = Math.Sqrt(this.X * this.X + this.Y * this.Y);
        if (num <= 1E-09)
          return;
        this.X *= value / num;
        this.Y *= value / num;
      }
    }


    public double LengthSquared => this.X * this.X + this.Y * this.Y;

    public static Vector2D AxisX => new Vector2D(1.0, 0.0);

    public static Vector2D AxisY => new Vector2D(0.0, 1.0);


    public double Angle => Math.Atan2(this.Y, this.X);

    public static double AngleBetween(Vector2D u, Vector2D v)
    {
      double d = u * v;
      Utility.LimitRange<double>(-1.0, ref d, 1.0);
      return Math.Acos(d);
    }

    public static double SignedAngleBetween(Vector2D u, Vector2D v)
    {
      return Math.Atan2(Vector2D.PerpDotProduct(u, v), Vector2D.Dot(u, v));
    }

    public static double PerpDotProduct(Vector2D u, Vector2D v) => u.X * v.Y - u.Y * v.X;

    public static Vector2D operator -(Vector2D u, Vector2D v) => new Vector2D(u.X - v.X, u.Y - v.Y);

    public static Vector2D operator +(Vector2D u, Vector2D v) => new Vector2D(u.X + v.X, u.Y + v.Y);

    public static double Dot(Vector2D u, Vector2D v) => u.X * v.X + u.Y * v.Y;

    public static double operator *(Vector2D u, Vector2D v) => u.X * v.X + u.Y * v.Y;

    public static bool operator ==(Vector2D left, Vector2D right)
    {
      return object.Equals((object) left, (object) right);
    }

    public static bool operator !=(Vector2D left, Vector2D right)
    {
      return !object.Equals((object) left, (object) right);
    }

    public static double Dot(Vector2D v, Point2D p) => v.X * p.X + v.Y * p.Y;

    public static double Dot(Point2D p, Vector2D v) => p.X * v.X + p.Y * v.Y;

    public void Zero() => this.X = this.Y = 0.0;

    public static bool AreCoincident(Vector2D u, Vector2D v)
    {
      return Vector2D.AreCoincident(u, v, Utility.TOL9);
    }

    public static bool AreCoincident(Vector2D u, Vector2D v, double tol)
    {
      return Math.Abs(1.0 - u * v) < tol;
    }

    public static bool AreOpposite(Vector2D u, Vector2D v)
    {
      return Vector2D.AreOpposite(u, v, Utility.TOL9);
    }

    public static bool AreOpposite(Vector2D u, Vector2D v, double tol)
    {
      return Math.Abs(u * v + 1.0) < tol;
    }

    public static bool AreParallel(Vector2D u, Vector2D v)
    {
      return Vector2D.AreParallel(u, v, Utility.TOL9);
    }

    public static bool AreParallel(Vector2D u, Vector2D v, double tol)
    {
      return Math.Abs(Math.Abs(u * v) - 1.0) < tol;
    }

    public static bool AreOrthogonal(Vector2D u, Vector2D v)
    {
      return Vector2D.AreOrthogonal(u, v, Utility.TOL9);
    }

    public static bool AreOrthogonal(Vector2D u, Vector2D v, double tol) => Math.Abs(u * v) < tol;

    public bool Equals(Vector2D other)
    {
      return Utility.Compare(other.X, this.X) == 0 && Utility.Compare(other.Y, this.Y) == 0;
    }

    public override bool Equals(object obj)
    {
      return (object) (obj as Vector2D) != null && this.Equals((Vector2D) obj);
    }

    public override int GetHashCode() => this.X.GetHashCode() * 397 ^ this.Y.GetHashCode();

    public override string ToString()
    {
      return string.Format("Vector2D: {0}, {1}, {2}, 
      (object) this.X, (object) this.Y, (object) this.Length);
    }


    public void Negate()
    {
      this.X = -this.X;
      this.Y = -this.Y;
    }

    public virtual void TransformBy(Transformation xform)
    {
      double[] numArray = xform.ActOnLeft(this.X, this.Y, 0.0, 0.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      this.X = num * numArray[0];
      this.Y = num * numArray[1];
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





