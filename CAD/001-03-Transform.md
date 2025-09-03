# Transformation
```csharp
namespace Geometry
{
  [Serializable]
  public class Transformation : ISerializable, ICloneable
  {
    private double[,] matrix = new double[4, 4];

    public Transformation() => this.matrix[3, 3] = 1.0;

    [DebuggerStepThrough]
    public Transformation(double d)
    {
      this.matrix[0, 0] = this.matrix[1, 1] = this.matrix[2, 2] = d;
      this.matrix[3, 3] = 1.0;
    }

    public Transformation(double[,] m) => Array.Copy((Array) m, (Array) this.matrix, 16);

    public Transformation(double[] m, bool byRow = true)
    {
      if (byRow)
      {
        this.matrix[0, 0] = m[0];
        this.matrix[0, 1] = m[1];
        this.matrix[0, 2] = m[2];
        this.matrix[0, 3] = m[3];
        this.matrix[1, 0] = m[4];
        this.matrix[1, 1] = m[5];
        this.matrix[1, 2] = m[6];
        this.matrix[1, 3] = m[7];
        this.matrix[2, 0] = m[8];
        this.matrix[2, 1] = m[9];
        this.matrix[2, 2] = m[10];
        this.matrix[2, 3] = m[11];
        this.matrix[3, 0] = m[12];
        this.matrix[3, 1] = m[13];
        this.matrix[3, 2] = m[14];
        this.matrix[3, 3] = m[15];
      }
      else
      {
        this.matrix[0, 0] = m[0];
        this.matrix[0, 1] = m[4];
        this.matrix[0, 2] = m[8];
        this.matrix[0, 3] = m[12];
        this.matrix[1, 0] = m[1];
        this.matrix[1, 1] = m[5];
        this.matrix[1, 2] = m[9];
        this.matrix[1, 3] = m[13];
        this.matrix[2, 0] = m[2];
        this.matrix[2, 1] = m[6];
        this.matrix[2, 2] = m[10];
        this.matrix[2, 3] = m[14];
        this.matrix[3, 0] = m[3];
        this.matrix[3, 1] = m[7];
        this.matrix[3, 2] = m[11];
        this.matrix[3, 3] = m[15];
      }
    }

    public Transformation(Point3D P, Vector3D X, Vector3D Y, Vector3D Z)
    {
      this.matrix[0, 0] = X.X;
      this.matrix[1, 0] = X.Y;
      this.matrix[2, 0] = X.Z;
      this.matrix[3, 0] = 0.0;
      this.matrix[0, 1] = Y.X;
      this.matrix[1, 1] = Y.Y;
      this.matrix[2, 1] = Y.Z;
      this.matrix[3, 1] = 0.0;
      this.matrix[0, 2] = Z.X;
      this.matrix[1, 2] = Z.Y;
      this.matrix[2, 2] = Z.Z;
      this.matrix[3, 2] = 0.0;
      this.matrix[0, 3] = P.X;
      this.matrix[1, 3] = P.Y;
      this.matrix[2, 3] = P.Z;
      this.matrix[3, 3] = 1.0;
    }

    protected Transformation(Transformation another)
    {
      Array.Copy((Array) another.matrix, (Array) this.matrix, 16);
    }

    public Transformation(SerializationInfo info, StreamingContext context)
    {
      this.matrix = (double[,]) info.GetValue(ErrMessage.GetString(425470378), typeof (double[,]));
    }

    public bool IsIdentity(double zeroTolerance)
    {
      return Math.Abs(1.0 - this.matrix[0, 0]) <= zeroTolerance 
        && Math.Abs(this.matrix[0, 1]) <= zeroTolerance 
        && Math.Abs(this.matrix[0, 2]) <= zeroTolerance 
        && Math.Abs(this.matrix[0, 3]) <= zeroTolerance 
        && Math.Abs(this.matrix[1, 0]) <= zeroTolerance 
        && Math.Abs(1.0 - this.matrix[1, 1]) <= zeroTolerance 
        && Math.Abs(this.matrix[1, 2]) <= zeroTolerance 
        && Math.Abs(this.matrix[1, 3]) <= zeroTolerance 
        && Math.Abs(this.matrix[2, 0]) <= zeroTolerance 
        && Math.Abs(this.matrix[2, 1]) <= zeroTolerance 
        && Math.Abs(1.0 - this.matrix[2, 2]) <= zeroTolerance 
        && Math.Abs(this.matrix[2, 3]) <= zeroTolerance 
        && Math.Abs(this.matrix[3, 0]) <= zeroTolerance 
        && Math.Abs(this.matrix[3, 1]) <= zeroTolerance 
        && Math.Abs(this.matrix[3, 2]) <= zeroTolerance 
        && Math.Abs(1.0 - this.matrix[3, 3]) <= zeroTolerance;
    }

    public bool IsIdentity()
    {
      return this.matrix[0, 0] == 1.0 && this.matrix[0, 1] == 0.0 
        && this.matrix[0, 2] == 0.0 && this.matrix[0, 3] == 0.0 
        && this.matrix[1, 0] == 0.0 && this.matrix[1, 1] == 1.0 
        && this.matrix[1, 2] == 0.0 && this.matrix[1, 3] == 0.0 
        && this.matrix[2, 0] == 0.0 && this.matrix[2, 1] == 0.0 
        && this.matrix[2, 2] == 1.0 && this.matrix[2, 3] == 0.0 
        && this.matrix[3, 0] == 0.0 && this.matrix[3, 1] == 0.0 
        && this.matrix[3, 2] == 0.0 && this.matrix[3, 3] == 1.0;
    }

    public virtual object Clone() => (object) new Transformation(this);

    protected bool Equals(Transformation other)
    {
      return ((IEnumerable<double>) this.MatrixAsVectorByRow).SequenceEqual<double>((IEnumerable<double>) other.MatrixAsVectorByRow);
    }

    public override bool Equals(object obj)
    {
      if (obj == null)
        return false;
      if ((object) this == obj)
        return true;
      return (object) (obj as Transformation) != null && this.Equals((Transformation) obj);
    }

    public override int GetHashCode()
    {
      int hashCode = 17;
      foreach (double num in this.MatrixAsVectorByRow)
        hashCode = hashCode * 31 + num.GetHashCode();
      return hashCode;
    }

    public static bool operator ==(Transformation left, Transformation right)
    {
      return object.Equals((object) left, (object) right);
    }

    public static bool operator !=(Transformation left, Transformation right)
    {
      return !object.Equals((object) left, (object) right);
    }

    public void Zero() => this.matrix = new double[4, 4];

    public void Identity()
    {
      this.matrix = new double[4, 4];
      this.matrix[0, 0] = this.matrix[1, 1] = this.matrix[2, 2] = this.matrix[3, 3] = 1.0;
    }

    public void Diagonal(double d)
    {
      this.matrix = new double[4, 4];
      this.matrix[0, 0] = this.matrix[1, 1] = this.matrix[2, 2] = this.matrix[3, 3] = d;
    }

    public void Scaling(double sx, double sy, double sz = 1.0)
    {
      this.matrix = new double[4, 4];
      this.matrix[0, 0] = sx;
      this.matrix[1, 1] = sy;
      this.matrix[2, 2] = sz;
      this.matrix[3, 3] = 1.0;
    }

    public void Scaling(Vector3D sv)
    {
      this.matrix = new double[4, 4];
      this.matrix[0, 0] = sv.X;
      this.matrix[1, 1] = sv.Y;
      this.matrix[2, 2] = sv.Z;
      this.matrix[3, 3] = 1.0;
    }

    public void Scaling(Point3D fixedPoint, double scaleFactor)
    {
      if (fixedPoint.X == 0.0 && fixedPoint.Y == 0.0 && fixedPoint.Z == 0.0)
      {
        this.Scaling(scaleFactor, scaleFactor, scaleFactor);
      }
      else
      {
        Transformation transformation1 = new Transformation();
        Transformation transformation2 = new Transformation();
        Transformation transformation3 = new Transformation();
        transformation1.Translation(Vector3D.Subtract(Point3D.Origin, fixedPoint));
        transformation2.Scaling(scaleFactor, scaleFactor, scaleFactor);
        transformation3.Translation(Vector3D.Subtract(fixedPoint, Point3D.Origin));
        this.matrix = (transformation3 * transformation2 * transformation1).matrix;
      }
    }

    public void Scaling(Point3D fixedPoint, double sx, double sy, double sz = 1.0)
    {
      if (fixedPoint.X == 0.0 && fixedPoint.Y == 0.0 && fixedPoint.Z == 0.0)
      {
        this.Scaling(sx, sy, sz);
      }
      else
      {
        Transformation transformation1 = new Transformation();
        Transformation transformation2 = new Transformation();
        Transformation transformation3 = new Transformation();
        transformation1.Translation(Vector3D.Subtract(Point3D.Origin, fixedPoint));
        transformation2.Scaling(sx, sy, sz);
        transformation3.Translation(Vector3D.Subtract(fixedPoint, Point3D.Origin));
        this.matrix = (transformation3 * transformation2 * transformation1).matrix;
      }
    }

    public void Translation(double dx, double dy, double dz = 0.0)
    {
      this.Identity();
      this.matrix[0, 3] = dx;
      this.matrix[1, 3] = dy;
      this.matrix[2, 3] = dz;
      this.matrix[3, 3] = 1.0;
    }

    public void Translation(Vector3D v)
    {
      this.Identity();
      this.matrix[0, 3] = v.X;
      this.matrix[1, 3] = v.Y;
      this.matrix[2, 3] = v.Z;
      this.matrix[3, 3] = 1.0;
    }

    public void PlanarProjection(Plane plane)
    {
      double[] numArray1 = new double[3]
      {
        plane.AxisX.X,
        plane.AxisX.Y,
        plane.AxisX.Z
      };
      double[] numArray2 = new double[3]
      {
        plane.AxisY.X,
        plane.AxisY.Y,
        plane.AxisY.Z
      };
      double[] numArray3 = new double[3]
      {
        plane.Origin.X,
        plane.Origin.Y,
        plane.Origin.Z
      };
      double[] numArray4 = new double[3];
      for (int index1 = 0; index1 < 3; ++index1)
      {
        for (int index2 = 0; index2 < 3; ++index2)
          this.matrix[index1, index2] = numArray1[index1] * numArray1[index2] + numArray2[index1] * numArray2[index2];
        numArray4[index1] = this.matrix[index1, 0] * numArray3[0] + this.matrix[index1, 1] * numArray3[1] + this.matrix[index1, 2] * numArray3[2];
      }
      for (int index = 0; index < 3; ++index)
      {
        this.matrix[3, index] = 0.0;
        this.matrix[index, 3] = numArray3[index] - numArray4[index];
      }
      this.matrix[3, 3] = 1.0;
    }

    public double[] ActOnLeft(double x, double y, double z, double w)
    {
      return new double[4]
      {
        this.matrix[0, 0] * x + this.matrix[0, 1] * y + this.matrix[0, 2] * z + this.matrix[0, 3] * w,
        this.matrix[1, 0] * x + this.matrix[1, 1] * y + this.matrix[1, 2] * z + this.matrix[1, 3] * w,
        this.matrix[2, 0] * x + this.matrix[2, 1] * y + this.matrix[2, 2] * z + this.matrix[2, 3] * w,
        this.matrix[3, 0] * x + this.matrix[3, 1] * y + this.matrix[3, 2] * z + this.matrix[3, 3] * w
      };
    }

    public double[] ActOnLeftOne(double x, double y, double z)
    {
      return new double[3]
      {
        this.matrix[0, 0] * x + this.matrix[0, 1] * y + this.matrix[0, 2] * z + this.matrix[0, 3],
        this.matrix[1, 0] * x + this.matrix[1, 1] * y + this.matrix[1, 2] * z + this.matrix[1, 3],
        this.matrix[2, 0] * x + this.matrix[2, 1] * y + this.matrix[2, 2] * z + this.matrix[2, 3]
      };
    }

    public double[] ActOnLeftZero(double x, double y, double z)
    {
      return new double[3]
      {
        this.matrix[0, 0] * x + this.matrix[0, 1] * y + this.matrix[0, 2] * z,
        this.matrix[1, 0] * x + this.matrix[1, 1] * y + this.matrix[1, 2] * z,
        this.matrix[2, 0] * x + this.matrix[2, 1] * y + this.matrix[2, 2] * z
      };
    }

    private double[] ActOnRightOne(
      double _param1,
      double _param2,
      double _param3,
      double _param4)
    {
      return new double[4]
      {
        this.matrix[0, 0] * _param1 + this.matrix[1, 0] * _param2 + this.matrix[2, 0] * _param3 + this.matrix[3, 0] * _param4,
        this.matrix[0, 1] * _param1 + this.matrix[1, 1] * _param2 + this.matrix[2, 1] * _param3 + this.matrix[3, 1] * _param4,
        this.matrix[0, 2] * _param1 + this.matrix[1, 2] * _param2 + this.matrix[2, 2] * _param3 + this.matrix[3, 2] * _param4,
        this.matrix[0, 3] * _param1 + this.matrix[1, 3] * _param2 + this.matrix[2, 3] * _param3 + this.matrix[3, 3] * _param4
      };
    }

    public static float[] ActOnLeftOne(float x, float y, float z, float[,] m)
    {
      return new float[3]
      {
        (float) ((double) m[0, 0] * (double) x + (double) m[0, 1] * (double) y + (double) m[0, 2] * (double) z) + m[0, 3],
        (float) ((double) m[1, 0] * (double) x + (double) m[1, 1] * (double) y + (double) m[1, 2] * (double) z) + m[1, 3],
        (float) ((double) m[2, 0] * (double) x + (double) m[2, 1] * (double) y + (double) m[2, 2] * (double) z) + m[2, 3]
      };
    }

    public static float[] ActOnLeftZero(float x, float y, float z, float[,] m)
    {
      return new float[3]
      {
        (float) ((double) m[0, 0] * (double) x + (double) m[0, 1] * (double) y + (double) m[0, 2] * (double) z),
        (float) ((double) m[1, 0] * (double) x + (double) m[1, 1] * (double) y + (double) m[1, 2] * (double) z),
        (float) ((double) m[2, 0] * (double) x + (double) m[2, 1] * (double) y + (double) m[2, 2] * (double) z)
      };
    }

    public float[,] GetFloatMatrix()
    {
      return new float[4, 4]
      {
        {
          (float) this.matrix[0, 0],
          (float) this.matrix[0, 1],
          (float) this.matrix[0, 2],
          (float) this.matrix[0, 3]
        },
        {
          (float) this.matrix[1, 0],
          (float) this.matrix[1, 1],
          (float) this.matrix[1, 2],
          (float) this.matrix[1, 3]
        },
        {
          (float) this.matrix[2, 0],
          (float) this.matrix[2, 1],
          (float) this.matrix[2, 2],
          (float) this.matrix[2, 3]
        },
        {
          (float) this.matrix[3, 0],
          (float) this.matrix[3, 1],
          (float) this.matrix[3, 2],
          (float) this.matrix[3, 3]
        }
      };
    }

    public static Point2D operator *(Transformation t, Point2D p)
    {
      double[] numArray = t.ActOnLeft(p.X, p.Y, 0.0, 1.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      return new Point2D(num * numArray[0], num * numArray[1]);
    }

    public static Point3D operator *(Transformation t, Point3D p)
    {
      double[] numArray = t.ActOnLeft(p.X, p.Y, p.Z, 1.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      return new Point3D(num * numArray[0], num * numArray[1], num * numArray[2]);
    }

    public static PointRGB operator *(Transformation t, PointRGB p)
    {
      double[] numArray = t.ActOnLeft(p.X, p.Y, p.Z, 1.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      return new PointRGB(num * numArray[0], num * numArray[1], num * numArray[2], p.R, p.G, p.B);
    }

    public static Point4D operator *(Transformation t, Point4D h)
    {
      double[] numArray = t.ActOnLeft(h.X, h.Y, h.Z, h.W);
      return new Point4D(numArray[0], numArray[1], numArray[2], numArray[3]);
    }

    public static Vector2D operator *(Transformation t, Vector2D v)
    {
      double[] numArray = t.ActOnLeft(v.X, v.Y, 0.0, 0.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      return new Vector2D(num * numArray[0], num * numArray[1]);
    }

    public static Vector3D operator *(Transformation t, Vector3D v)
    {
      double[] numArray = t.ActOnLeft(v.X, v.Y, v.Z, 0.0);
      double num = numArray[3] != 0.0 ? 1.0 / numArray[3] : 1.0;
      return new Vector3D(num * numArray[0], num * numArray[1], num * numArray[2]);
    }

    public void Transpose()
    {
      Utility.Swap<double>(ref this.matrix.Address(0, 1), ref this.matrix.Address(1, 0));
      Utility.Swap<double>(ref this.matrix.Address(0, 2), ref this.matrix.Address(2, 0));
      Utility.Swap<double>(ref this.matrix.Address(0, 3), ref this.matrix.Address(3, 0));
      Utility.Swap<double>(ref this.matrix.Address(1, 2), ref this.matrix.Address(2, 1));
      Utility.Swap<double>(ref this.matrix.Address(1, 3), ref this.matrix.Address(3, 1));
      Utility.Swap<double>(ref this.matrix.Address(2, 3), ref this.matrix.Address(3, 2));
    }

    public void Invert()
    {
      double[,] numArray = new double[4, 4];
      Array.Copy((Array) this.matrix, (Array) numArray, 16);
      this.matrix = devDept.Geometry.Matrix.Inverse4(numArray);
    }

    public void Rotation(double angleInRadians, Vector3D axis)
    {
      this.UpdateMatrix(Math.Sin(angleInRadians), Math.Cos(angleInRadians), axis, Point3D.Origin);
    }

    public void Rotation(double angleInRadians, Point3D axisStart, Point3D axisEnd)
    {
      this.UpdateMatrix(Math.Sin(angleInRadians), Math.Cos(angleInRadians), Vector3D.Subtract(axisEnd, axisStart), axisStart);
    }

    public void Rotation(double angleInRadians, Vector3D axis, Point3D center)
    {
      this.UpdateMatrix(Math.Sin(angleInRadians), Math.Cos(angleInRadians), axis, center);
    }

    public void Rotation(Vector3D startDir, Vector3D endDir, Point3D center)
    {
      Vector3D vector3D1 = (Vector3D) startDir.Clone();
      vector3D1.Normalize();
      Vector3D b = (Vector3D) endDir.Clone();
      b.Normalize();
      double num1 = vector3D1 * b;
      Vector3D vector3D2 = Vector3D.Cross(vector3D1, b);
      double num2 = vector3D2.Length;
      vector3D2.Normalize();
      if (0.0 == num2)
      {
        vector3D2.PerpendicularTo(vector3D1);
        vector3D2.Normalize();
        num2 = 0.0;
        num1 = num1 < 0.0 ? -1.0 : 1.0;
      }
      this.UpdateMatrix(num2, num1, vector3D2, center);
    }

    internal void UpdateMatrix(
      double _param1,
      double _param2,
      Vector3D _param3,
      Point3D _param4)
    {
      this.Identity();
      if (Math.Abs(_param1) >= 0.9999999850988388 && Math.Abs(_param2) <= 1.490116119385E-08)
      {
        _param2 = 0.0;
        _param1 = _param1 >= 0.0 ? 1.0 : -1.0;
      }
      else if (Math.Abs(_param2) >= 0.9999999850988388 && Math.Abs(_param1) <= 1.490116119385E-08)
      {
        _param1 = 0.0;
        _param2 = _param2 >= 0.0 ? 1.0 : -1.0;
      }
      else if (Math.Abs(_param2 * _param2 + _param1 * _param1 - 1.0) > 1.490116119385E-08)
      {
        Vector2D vector2D = new Vector2D(_param2, _param1);
        vector2D.Normalize();
        _param2 = vector2D.X;
        _param1 = vector2D.Y;
      }
      else
      {
        if (_param2 > 1.0)
          _param2 = 1.0;
        else if (_param2 < -1.0)
          _param2 = -1.0;
        if (_param1 > 1.0)
          _param1 = 1.0;
        else if (_param1 < -1.0)
          _param1 = -1.0;
      }
      if (_param1 == 0.0 && _param2 == 1.0)
        return;
      double num = 1.0 - _param2;
      Vector3D vector3D = (Vector3D) _param3.Clone();
      if (Math.Abs(vector3D.LengthSquared - 1.0) > 2.220446049250313E-16)
        vector3D.Normalize();
      this.matrix[0, 0] = vector3D.X * vector3D.X * num + _param2;
      this.matrix[0, 1] = vector3D.X * vector3D.Y * num - vector3D.Z * _param1;
      this.matrix[0, 2] = vector3D.X * vector3D.Z * num + vector3D.Y * _param1;
      this.matrix[1, 0] = vector3D.Y * vector3D.X * num + vector3D.Z * _param1;
      this.matrix[1, 1] = vector3D.Y * vector3D.Y * num + _param2;
      this.matrix[1, 2] = vector3D.Y * vector3D.Z * num - vector3D.X * _param1;
      this.matrix[2, 0] = vector3D.Z * vector3D.X * num - vector3D.Y * _param1;
      this.matrix[2, 1] = vector3D.Z * vector3D.Y * num + vector3D.X * _param1;
      this.matrix[2, 2] = vector3D.Z * vector3D.Z * num + _param2;
      if (_param4.X != 0.0 || _param4.Y != 0.0 || _param4.Z != 0.0)
      {
        this.matrix[0, 3] = -((this.matrix[0, 0] - 1.0) * _param4.X + this.matrix[0, 1] * _param4.Y + this.matrix[0, 2] * _param4.Z);
        this.matrix[1, 3] = -(this.matrix[1, 0] * _param4.X + (this.matrix[1, 1] - 1.0) * _param4.Y + this.matrix[1, 2] * _param4.Z);
        this.matrix[2, 3] = -(this.matrix[2, 0] * _param4.X + this.matrix[2, 1] * _param4.Y + (this.matrix[2, 2] - 1.0) * _param4.Z);
      }
      this.matrix[3, 0] = this.matrix[3, 1] = this.matrix[3, 2] = 0.0;
      this.matrix[3, 3] = 1.0;
    }

    public void Rotation(
      Vector3D X0,
      Vector3D Y0,
      Vector3D Z0,
      Vector3D X1,
      Vector3D Y1,
      Vector3D Z1)
    {
      this.matrix = (new Transformation()
      {
        [0, 0] = X1.X,
        [0, 1] = Y1.X,
        [0, 2] = Z1.X,
        [1, 0] = X1.Y,
        [1, 1] = Y1.Y,
        [1, 2] = Z1.Y,
        [2, 0] = X1.Z,
        [2, 1] = Y1.Z,
        [2, 2] = Z1.Z,
        [3, 3] = 1.0
      } * new Transformation()
      {
        [0, 0] = X0.X,
        [0, 1] = X0.Y,
        [0, 2] = X0.Z,
        [1, 0] = Y0.X,
        [1, 1] = Y0.Y,
        [1, 2] = Y0.Z,
        [2, 0] = Z0.X,
        [2, 1] = Z0.Y,
        [2, 2] = Z0.Z,
        [3, 3] = 1.0
      }).matrix;
    }

    public void Rotation(Plane plane0, Plane plane1)
    {
      this.Rotation(plane0.Origin, plane0.AxisX, plane0.AxisY, plane0.AxisZ, plane1.Origin, plane1.AxisX, plane1.AxisY, plane1.AxisZ);
    }

    public void Rotation(
      Point3D P0,
      Vector3D X0,
      Vector3D Y0,
      Vector3D Z0,
      Point3D P1,
      Vector3D X1,
      Vector3D Y1,
      Vector3D Z1)
    {
      Transformation transformation1 = new Transformation();
      transformation1.Translation(-P0.X, -P0.Y, -P0.Z);
      Transformation transformation2 = new Transformation();
      transformation2.Rotation(X0, Y0, Z0, X1, Y1, Z1);
      Transformation transformation3 = new Transformation();
      transformation3.Translation(P1.X, P1.Y, P1.Z);
      this.matrix = (transformation3 * transformation2 * transformation1).matrix;
    }

    public void Mirror(Plane plane) => this.Mirror(plane.Origin, plane.AxisZ);

    public void Mirror(Point3D P, Vector3D N)
    {
      Vector3D vector3D1 = (Vector3D) N.Clone();
      vector3D1.Normalize();
      Vector3D vector3D2 = 2.0 * (vector3D1.X * P.X + vector3D1.Y * P.Y + vector3D1.Z * P.Z) * vector3D1;
      this.matrix[0, 0] = 1.0 - 2.0 * vector3D1.X * vector3D1.X;
      this.matrix[0, 1] = -2.0 * vector3D1.X * vector3D1.Y;
      this.matrix[0, 2] = -2.0 * vector3D1.X * vector3D1.Z;
      this.matrix[0, 3] = vector3D2.X;
      this.matrix[1, 0] = -2.0 * vector3D1.Y * vector3D1.X;
      this.matrix[1, 1] = 1.0 - 2.0 * vector3D1.Y * vector3D1.Y;
      this.matrix[1, 2] = -2.0 * vector3D1.Y * vector3D1.Z;
      this.matrix[1, 3] = vector3D2.Y;
      this.matrix[2, 0] = -2.0 * vector3D1.Z * vector3D1.X;
      this.matrix[2, 1] = -2.0 * vector3D1.Z * vector3D1.Y;
      this.matrix[2, 2] = 1.0 - 2.0 * vector3D1.Z * vector3D1.Z;
      this.matrix[2, 3] = vector3D2.Z;
      this.matrix[3, 0] = 0.0;
      this.matrix[3, 1] = 0.0;
      this.matrix[3, 2] = 0.0;
      this.matrix[3, 3] = 1.0;
    }

    public double this[int m, int n]
    {
      get => this.matrix[m, n];
      set => this.matrix[m, n] = value;
    }

    public static Transformation operator *(Transformation left, Transformation right)
    {
      Transformation transformation = new Transformation();
      double[,] matrix = transformation.matrix;
      matrix[0, 0] = left.matrix[0, 0] * right.matrix[0, 0] + left.matrix[0, 1] * right.matrix[1, 0] + left.matrix[0, 2] * right.matrix[2, 0] + left.matrix[0, 3] * right.matrix[3, 0];
      matrix[0, 1] = left.matrix[0, 0] * right.matrix[0, 1] + left.matrix[0, 1] * right.matrix[1, 1] + left.matrix[0, 2] * right.matrix[2, 1] + left.matrix[0, 3] * right.matrix[3, 1];
      matrix[0, 2] = left.matrix[0, 0] * right.matrix[0, 2] + left.matrix[0, 1] * right.matrix[1, 2] + left.matrix[0, 2] * right.matrix[2, 2] + left.matrix[0, 3] * right.matrix[3, 2];
      matrix[0, 3] = left.matrix[0, 0] * right.matrix[0, 3] + left.matrix[0, 1] * right.matrix[1, 3] + left.matrix[0, 2] * right.matrix[2, 3] + left.matrix[0, 3] * right.matrix[3, 3];
      matrix[1, 0] = left.matrix[1, 0] * right.matrix[0, 0] + left.matrix[1, 1] * right.matrix[1, 0] + left.matrix[1, 2] * right.matrix[2, 0] + left.matrix[1, 3] * right.matrix[3, 0];
      matrix[1, 1] = left.matrix[1, 0] * right.matrix[0, 1] + left.matrix[1, 1] * right.matrix[1, 1] + left.matrix[1, 2] * right.matrix[2, 1] + left.matrix[1, 3] * right.matrix[3, 1];
      matrix[1, 2] = left.matrix[1, 0] * right.matrix[0, 2] + left.matrix[1, 1] * right.matrix[1, 2] + left.matrix[1, 2] * right.matrix[2, 2] + left.matrix[1, 3] * right.matrix[3, 2];
      matrix[1, 3] = left.matrix[1, 0] * right.matrix[0, 3] + left.matrix[1, 1] * right.matrix[1, 3] + left.matrix[1, 2] * right.matrix[2, 3] + left.matrix[1, 3] * right.matrix[3, 3];
      matrix[2, 0] = left.matrix[2, 0] * right.matrix[0, 0] + left.matrix[2, 1] * right.matrix[1, 0] + left.matrix[2, 2] * right.matrix[2, 0] + left.matrix[2, 3] * right.matrix[3, 0];
      matrix[2, 1] = left.matrix[2, 0] * right.matrix[0, 1] + left.matrix[2, 1] * right.matrix[1, 1] + left.matrix[2, 2] * right.matrix[2, 1] + left.matrix[2, 3] * right.matrix[3, 1];
      matrix[2, 2] = left.matrix[2, 0] * right.matrix[0, 2] + left.matrix[2, 1] * right.matrix[1, 2] + left.matrix[2, 2] * right.matrix[2, 2] + left.matrix[2, 3] * right.matrix[3, 2];
      matrix[2, 3] = left.matrix[2, 0] * right.matrix[0, 3] + left.matrix[2, 1] * right.matrix[1, 3] + left.matrix[2, 2] * right.matrix[2, 3] + left.matrix[2, 3] * right.matrix[3, 3];
      matrix[3, 0] = left.matrix[3, 0] * right.matrix[0, 0] + left.matrix[3, 1] * right.matrix[1, 0] + left.matrix[3, 2] * right.matrix[2, 0] + left.matrix[3, 3] * right.matrix[3, 0];
      matrix[3, 1] = left.matrix[3, 0] * right.matrix[0, 1] + left.matrix[3, 1] * right.matrix[1, 1] + left.matrix[3, 2] * right.matrix[2, 1] + left.matrix[3, 3] * right.matrix[3, 1];
      matrix[3, 2] = left.matrix[3, 0] * right.matrix[0, 2] + left.matrix[3, 1] * right.matrix[1, 2] + left.matrix[3, 2] * right.matrix[2, 2] + left.matrix[3, 3] * right.matrix[3, 2];
      matrix[3, 3] = left.matrix[3, 0] * right.matrix[0, 3] + left.matrix[3, 1] * right.matrix[1, 3] + left.matrix[3, 2] * right.matrix[2, 3] + left.matrix[3, 3] * right.matrix[3, 3];
      return transformation;
    }

    public static Transformation operator +(Transformation left, Transformation right)
    {
      return new Transformation()
      {
        [0, 0] = left[0, 0] + right[0, 0],
        [0, 1] = left[0, 1] + right[0, 1],
        [0, 2] = left[0, 2] + right[0, 2],
        [0, 3] = left[0, 3] + right[0, 3],
        [1, 0] = left[1, 0] + right[1, 0],
        [1, 1] = left[1, 1] + right[1, 1],
        [1, 2] = left[1, 2] + right[1, 2],
        [1, 3] = left[1, 3] + right[1, 3],
        [2, 0] = left[2, 0] + right[2, 0],
        [2, 1] = left[2, 1] + right[2, 1],
        [2, 2] = left[2, 2] + right[2, 2],
        [2, 3] = left[2, 3] + right[2, 3],
        [3, 0] = left[3, 0] + right[3, 0],
        [3, 1] = left[3, 1] + right[3, 1],
        [3, 2] = left[3, 2] + right[3, 2],
        [3, 3] = left[3, 3] + right[3, 3]
      };
    }

    public static Transformation operator -(Transformation left, Transformation right)
    {
      return new Transformation()
      {
        [0, 0] = left[0, 0] - right[0, 0],
        [0, 1] = left[0, 1] - right[0, 1],
        [0, 2] = left[0, 2] - right[0, 2],
        [0, 3] = left[0, 3] - right[0, 3],
        [1, 0] = left[1, 0] - right[1, 0],
        [1, 1] = left[1, 1] - right[1, 1],
        [1, 2] = left[1, 2] - right[1, 2],
        [1, 3] = left[1, 3] - right[1, 3],
        [2, 0] = left[2, 0] - right[2, 0],
        [2, 1] = left[2, 1] - right[2, 1],
        [2, 2] = left[2, 2] - right[2, 2],
        [2, 3] = left[2, 3] - right[2, 3],
        [3, 0] = left[3, 0] - right[3, 0],
        [3, 1] = left[3, 1] - right[3, 1],
        [3, 2] = left[3, 2] - right[3, 2],
        [3, 3] = left[3, 3] - right[3, 3]
      };
    }

    public bool ChangeBasis(Plane initial, Plane final)
    {
      return this.ChangeBasis(initial.Origin, initial.AxisX, initial.AxisY, 
        initial.AxisZ, final.Origin, final.AxisX, final.AxisY, final.AxisZ);
    }

    public bool ChangeBasis(
      Vector3D X0,
      Vector3D Y0,
      Vector3D Z0,
      Vector3D X1,
      Vector3D Y1,
      Vector3D Z1)
    {
      this.Zero();
      this.matrix[3, 3] = 1.0;
      double num1 = X1 * Y1;
      double num2 = X1 * Z1;
      double num3 = Y1 * Z1;
      double[,] numArray = new double[3, 6]
      {
        {
          X1 * X1,
          num1,
          num2,
          X1 * X0,
          X1 * Y0,
          X1 * Z0
        },
        {
          num1,
          Y1 * Y1,
          num3,
          Y1 * X0,
          Y1 * Y0,
          Y1 * Z0
        },
        {
          num2,
          num3,
          Z1 * Z1,
          Z1 * X0,
          Z1 * Y0,
          Z1 * Z0
        }
      };
      int index1 = numArray[0, 0] >= numArray[1, 1] ? 0 : 1;
      if (numArray[2, 2] > numArray[index1, index1])
        index1 = 2;
      int index2 = (index1 + 1) % 3;
      int index3 = (index2 + 1) % 3;
      if (numArray[index1, index1] == 0.0)
        return false;
      double num4 = 1.0 / numArray[index1, index1];
      numArray[index1, 0] *= num4;
      numArray[index1, 1] *= num4;
      numArray[index1, 2] *= num4;
      numArray[index1, 3] *= num4;
      numArray[index1, 4] *= num4;
      numArray[index1, 5] *= num4;
      numArray[index1, index1] = 1.0;
      if (numArray[index2, index1] != 0.0)
      {
        double num5 = -numArray[index2, index1];
        numArray[index2, 0] += num5 * numArray[index1, 0];
        numArray[index2, 1] += num5 * numArray[index1, 1];
        numArray[index2, 2] += num5 * numArray[index1, 2];
        numArray[index2, 3] += num5 * numArray[index1, 3];
        numArray[index2, 4] += num5 * numArray[index1, 4];
        numArray[index2, 5] += num5 * numArray[index1, 5];
        numArray[index2, index1] = 0.0;
      }
      if (numArray[index3, index1] != 0.0)
      {
        double num6 = -numArray[index3, index1];
        numArray[index3, 0] += num6 * numArray[index1, 0];
        numArray[index3, 1] += num6 * numArray[index1, 1];
        numArray[index3, 2] += num6 * numArray[index1, 2];
        numArray[index3, 3] += num6 * numArray[index1, 3];
        numArray[index3, 4] += num6 * numArray[index1, 4];
        numArray[index3, 5] += num6 * numArray[index1, 5];
        numArray[index3, index1] = 0.0;
      }
      if (Math.Abs(numArray[index2, index2]) < Math.Abs(numArray[index3, index3]))
      {
        int num7 = index2;
        index2 = index3;
        index3 = num7;
      }
      if (numArray[index2, index2] == 0.0)
        return false;
      double num8 = 1.0 / numArray[index2, index2];
      numArray[index2, 0] *= num8;
      numArray[index2, 1] *= num8;
      numArray[index2, 2] *= num8;
      numArray[index2, 3] *= num8;
      numArray[index2, 4] *= num8;
      numArray[index2, 5] *= num8;
      numArray[index2, index2] = 1.0;
      if (numArray[index1, index2] != 0.0)
      {
        double num9 = -numArray[index1, index2];
        numArray[index1, 0] += num9 * numArray[index2, 0];
        numArray[index1, 1] += num9 * numArray[index2, 1];
        numArray[index1, 2] += num9 * numArray[index2, 2];
        numArray[index1, 3] += num9 * numArray[index2, 3];
        numArray[index1, 4] += num9 * numArray[index2, 4];
        numArray[index1, 5] += num9 * numArray[index2, 5];
        numArray[index1, index2] = 0.0;
      }
      if (numArray[index3, index2] != 0.0)
      {
        double num10 = -numArray[index3, index2];
        numArray[index3, 0] += num10 * numArray[index2, 0];
        numArray[index3, 1] += num10 * numArray[index2, 1];
        numArray[index3, 2] += num10 * numArray[index2, 2];
        numArray[index3, 3] += num10 * numArray[index2, 3];
        numArray[index3, 4] += num10 * numArray[index2, 4];
        numArray[index3, 5] += num10 * numArray[index2, 5];
        numArray[index3, index2] = 0.0;
      }
      if (numArray[index3, index3] == 0.0)
        return false;
      double num11 = 1.0 / numArray[index3, index3];
      numArray[index3, 0] *= num11;
      numArray[index3, 1] *= num11;
      numArray[index3, 2] *= num11;
      numArray[index3, 3] *= num11;
      numArray[index3, 4] *= num11;
      numArray[index3, 5] *= num11;
      numArray[index3, index3] = 1.0;
      if (numArray[index1, index3] != 0.0)
      {
        double num12 = -numArray[index1, index3];
        numArray[index1, 0] += num12 * numArray[index3, 0];
        numArray[index1, 1] += num12 * numArray[index3, 1];
        numArray[index1, 2] += num12 * numArray[index3, 2];
        numArray[index1, 3] += num12 * numArray[index3, 3];
        numArray[index1, 4] += num12 * numArray[index3, 4];
        numArray[index1, 5] += num12 * numArray[index3, 5];
        numArray[index1, index3] = 0.0;
      }
      if (numArray[index2, index3] != 0.0)
      {
        double num13 = -numArray[index2, index3];
        numArray[index2, 0] += num13 * numArray[index3, 0];
        numArray[index2, 1] += num13 * numArray[index3, 1];
        numArray[index2, 2] += num13 * numArray[index3, 2];
        numArray[index2, 3] += num13 * numArray[index3, 3];
        numArray[index2, 4] += num13 * numArray[index3, 4];
        numArray[index2, 5] += num13 * numArray[index3, 5];
        numArray[index2, index3] = 0.0;
      }
      this.matrix[0, 0] = numArray[0, 3];
      this.matrix[0, 1] = numArray[0, 4];
      this.matrix[0, 2] = numArray[0, 5];
      this.matrix[1, 0] = numArray[1, 3];
      this.matrix[1, 1] = numArray[1, 4];
      this.matrix[1, 2] = numArray[1, 5];
      this.matrix[2, 0] = numArray[2, 3];
      this.matrix[2, 1] = numArray[2, 4];
      this.matrix[2, 2] = numArray[2, 5];
      return true;
    }

    public bool ChangeBasis(
      Point3D P0,
      Vector3D X0,
      Vector3D Y0,
      Vector3D Z0,
      Point3D P1,
      Vector3D X1,
      Vector3D Y1,
      Vector3D Z1)
    {
      Transformation transformation1 = new Transformation(P0, X0, Y0, Z0);
      Transformation transformation2 = new Transformation();
      transformation2.Translation(-P1.X, -P1.Y, -P1.Z);
      Transformation transformation3 = new Transformation();
      int num = transformation3.ChangeBasis(Vector3D.AxisX, Vector3D.AxisY, Vector3D.AxisZ, X1, Y1, Z1) ? 1 : 0;
      this.matrix = (transformation3 * transformation2 * transformation1).matrix;
      return num != 0;
    }

    public static void AutocadOCS(Vector3D normal, out Vector3D xAxis, out Vector3D yAxis)
    {
      Vector3D vector3D = (Vector3D) normal.Clone();
      vector3D.Normalize();
      Vector3D a1 = new Vector3D(0.0, 1.0, 0.0);
      Vector3D a2 = new Vector3D(0.0, 0.0, 1.0);
      xAxis = Math.Abs(vector3D.X) >= 1.0 / 64.0 || Math.Abs(vector3D.Y) >= 1.0 / 64.0 
        ? Vector3D.Cross(a2, vector3D) : Vector3D.Cross(a1, vector3D);
      xAxis.Normalize();
      yAxis = Vector3D.Cross(vector3D, xAxis);
      yAxis.Normalize();
    }

    public bool IsTranslation
    {
      get
      {
        return this.matrix[0, 0] == 1.0 && this.matrix[1, 1] == 1.0 && this.matrix[2, 2] == 1.0 
          && this.matrix[3, 3] == 1.0 && this.matrix[0, 1] == 0.0 && this.matrix[0, 2] == 0.0  
          && this.matrix[1, 0] == 0.0 && this.matrix[2, 0] == 0.0 && this.matrix[1, 2] == 0.0 
          && this.matrix[2, 1] == 0.0 && this.matrix[3, 0] == 0.0 && this.matrix[3, 1] == 0.0 && this.matrix[3, 2] == 0.0;
      }
    }

    public bool IsScaled
    {
      get
      {
        Vector3D vector3D1 = new Vector3D(this.Matrix[0, 0], this.Matrix[1, 0], this.Matrix[2, 0]);
        Vector3D vector3D2 = new Vector3D(this.Matrix[0, 1], this.Matrix[1, 1], this.Matrix[2, 1]);
        Vector3D vector3D3 = new Vector3D(this.Matrix[0, 2], this.Matrix[1, 2], this.Matrix[2, 2]);
        return Utility.Compare(1E-12, vector3D1.LengthSquared, 1.0) != 0 
          || Utility.Compare(1E-12, vector3D2.LengthSquared, 1.0) != 0 
          || Utility.Compare(1E-12, vector3D3.LengthSquared, 1.0) != 0;
      }
    }

    public double[,] Matrix
    {
      get => this.matrix;
      set => Array.Copy((Array) value, (Array) this.matrix, 16);
    }

    public double[] MatrixAsVectorByRow
    {
      get
      {
        return new double[16]
        {
          this.matrix[0, 0],
          this.matrix[0, 1],
          this.matrix[0, 2],
          this.matrix[0, 3],
          this.matrix[1, 0],
          this.matrix[1, 1],
          this.matrix[1, 2],
          this.matrix[1, 3],
          this.matrix[2, 0],
          this.matrix[2, 1],
          this.matrix[2, 2],
          this.matrix[2, 3],
          this.matrix[3, 0],
          this.matrix[3, 1],
          this.matrix[3, 2],
          this.matrix[3, 3]
        };
      }
    }

    public double[] MatrixAsVectorByColumn
    {
      get
      {
        return new double[16]
        {
          this.matrix[0, 0],
          this.matrix[1, 0],
          this.matrix[2, 0],
          this.matrix[3, 0],
          this.matrix[0, 1],
          this.matrix[1, 1],
          this.matrix[2, 1],
          this.matrix[3, 1],
          this.matrix[0, 2],
          this.matrix[1, 2],
          this.matrix[2, 2],
          this.matrix[3, 2],
          this.matrix[0, 3],
          this.matrix[1, 3],
          this.matrix[2, 3],
          this.matrix[3, 3]
        };
      }
    }

    public float[] MatrixAsVectorFloatByColumn
    {
      get
      {
        float[] vectorFloatByColumn = new float[16];
        int num = 0;
        for (int index1 = 0; index1 < 4; ++index1)
        {
          for (int index2 = 0; index2 < 4; ++index2)
            vectorFloatByColumn[num++] = (float) this.matrix[index2, index1];
        }
        return vectorFloatByColumn;
      }
    }

    public double ScaleFactorX
    {
      get
      {
        double num1 = this.matrix[0, 0];
        double num2 = this.matrix[1, 0];
        double num3 = this.matrix[2, 0];
        return Math.Sqrt(num1 * num1 + num2 * num2 + num3 * num3);
      }
    }

    public double ScaleFactorY
    {
      get
      {
        double num1 = this.matrix[0, 1];
        double num2 = this.matrix[1, 1];
        double num3 = this.matrix[2, 1];
        return Math.Sqrt(num1 * num1 + num2 * num2 + num3 * num3);
      }
    }

    public double ScaleFactorZ
    {
      get
      {
        double num1 = this.matrix[0, 2];
        double num2 = this.matrix[1, 2];
        double num3 = this.matrix[2, 2];
        return Math.Sqrt(num1 * num1 + num2 * num2 + num3 * num3);
      }
    }

    public bool IsScaleFactorUniform()
    {
      double num1 = Math.Abs(this.ScaleFactorX);
      double num2 = Math.Abs(this.ScaleFactorY);
      double num3 = Math.Abs(this.ScaleFactorZ);
      double num4 = num2;
      return Math.Abs(num1 - num4) < Utility.TOL9 && Math.Abs(num2 - num3) < Utility.TOL9;
    }

    public bool IsScaleFactorUniformForPlanar(Plane pl, ref double scaleFactor)
    {
      double num1 = Math.Abs(this.ScaleFactorX);
      double num2 = Math.Abs(this.ScaleFactorY);
      double num3 = Math.Abs(this.ScaleFactorZ);
      if (Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisZ))
        return Math.Abs(num1 - num2) < Utility.TOL9;
      if (Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisY))
        return Math.Abs(num1 - num3) < Utility.TOL9;
      if (!Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisX))
        return false;
      scaleFactor = this.ScaleFactorY;
      return Math.Abs(num3 - num2) < Utility.TOL9;
    }

    public bool IsReversed() => devDept.Geometry.Matrix.Determinant3(this.Matrix) < 0.0;

    public string Dump()
    {
      StringBuilder stringBuilder = new StringBuilder();
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[0, 0]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[0, 1]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[0, 2]));
      stringBuilder.Append(string.Format("{0}", (object) this.matrix[0, 3]));
      stringBuilder.Append(Environment.NewLine);
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[1, 0]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[1, 1]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[1, 2]));
      stringBuilder.Append(string.Format("{0}", (object) this.matrix[1, 3]));
      stringBuilder.Append(Environment.NewLine);
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[2, 0]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[2, 1]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[2, 2]));
      stringBuilder.Append(string.Format("{0}", (object) this.matrix[2, 3]));
      stringBuilder.Append(Environment.NewLine);
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[3, 0]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[3, 1]));
      stringBuilder.Append(string.Format("{0}, ", (object) this.matrix[3, 2]));
      stringBuilder.Append(string.Format("{0}", (object) this.matrix[3, 3]));
      return stringBuilder.ToString();
    }



    public bool EqualScaleFactors()
    {
      double scaleFactorX = this.ScaleFactorX;
      double scaleFactorY = this.ScaleFactorY;
      if (!Utility.AreEqual(scaleFactorX, scaleFactorY, Math.Max(Math.Abs(scaleFactorX), Math.Abs(scaleFactorY))))
        return false;
      double scaleFactorZ = this.ScaleFactorZ;
      return Utility.AreEqual(scaleFactorY, scaleFactorZ, Math.Max(Math.Abs(scaleFactorY), Math.Abs(scaleFactorZ)));
    }


    public Transformation GetTransformationForNormals()
    {
      Transformation transformationForNormals = new Transformation(this);
      transformationForNormals.Matrix[0, 3] = transformationForNormals.Matrix[1, 3] = transformationForNormals.Matrix[2, 3] = 0.0;
      transformationForNormals.Invert();
      transformationForNormals.Transpose();
      return transformationForNormals;
    }
  }
}
```