# Transformation
```csharp
using System;
using System.Collections.Generic;
using System.ComponentModel;
using System.Diagnostics;
using System.IO;
using System.Linq;
using System.Runtime.Serialization;
using System.Text;

namespace Geometry
{
  [Serializable]
  public class Transformation : ISerializable, ICloneable, IEquatable<Transformation>
  {
    // 내부 4x4 행렬 (row-major)
    private double[,] _m = new double[4, 4];

    #region 생성자

    public Transformation()
    {
      _m[0, 0] = 1.0; _m[1, 1] = 1.0; _m[2, 2] = 1.0; _m[3, 3] = 1.0;
    }

    [DebuggerStepThrough]
    public Transformation(double diagonal)
    {
      _m[0, 0] = diagonal; _m[1, 1] = diagonal; _m[2, 2] = diagonal; _m[3, 3] = 1.0;
    }

    public Transformation(double[,] m)
    {
      if (m == null || m.GetLength(0) != 4 || m.GetLength(1) != 4)
        throw new ArgumentException("Transformation(double[,]) requires 4x4 matrix");
      Array.Copy(m, _m, 16);
    }

    public Transformation(double[] flat16, bool byRow = true)
    {
      if (flat16 == null || flat16.Length < 16)
        throw new ArgumentException("Transformation(double[]) requires length >= 16");

      if (byRow)
      {
        int k = 0;
        for (int r = 0; r < 4; r++)
          for (int c = 0; c < 4; c++)
            _m[r, c] = flat16[k++];
      }
      else
      {
        int k = 0;
        for (int c = 0; c < 4; c++)
          for (int r = 0; r < 4; r++)
            _m[r, c] = flat16[k++];
      }
    }

    /// <summary>원점/기저로부터 변환 생성 (열은 X,Y,Z 축, 마지막 열은 위치)</summary>
    public Transformation(Point3D origin, Vector3D axisX, Vector3D axisY, Vector3D axisZ)
    {
      _m[0, 0] = axisX.X; _m[1, 0] = axisX.Y; _m[2, 0] = axisX.Z; _m[3, 0] = 0.0;
      _m[0, 1] = axisY.X; _m[1, 1] = axisY.Y; _m[2, 1] = axisY.Z; _m[3, 1] = 0.0;
      _m[0, 2] = axisZ.X; _m[1, 2] = axisZ.Y; _m[2, 2] = axisZ.Z; _m[3, 2] = 0.0;
      _m[0, 3] = origin.X; _m[1, 3] = origin.Y; _m[2, 3] = origin.Z; _m[3, 3] = 1.0;
    }

    protected Transformation(Transformation other)
    {
      Array.Copy(other._m, _m, 16);
    }

    #endregion

    #region 동등성/해시/직렬화

    public virtual object Clone() => new Transformation(this);

    public bool Equals(Transformation other)
    {
      if (ReferenceEquals(other, null)) return false;
      return MatrixAsVectorByRow.SequenceEqual(other.MatrixAsVectorByRow);
    }

    public override bool Equals(object obj) => obj is Transformation t && Equals(t);

    public override int GetHashCode()
    {
      int hc = 17;
      foreach (double v in MatrixAsVectorByRow)
        hc = unchecked(hc * 31 + v.GetHashCode());
      return hc;
    }

    public static bool operator ==(Transformation left, Transformation right) => Equals(left, right);
    public static bool operator !=(Transformation left, Transformation right) => !Equals(left, right);

    #endregion

    #region 상태/프로퍼티

    public double[,] Matrix
    {
      get => _m;
      set
      {
        if (value == null || value.GetLength(0) != 4 || value.GetLength(1) != 4)
          throw new ArgumentException("Matrix must be 4x4");
        Array.Copy(value, _m, 16);
      }
    }

    public double this[int r, int c]
    {
      get => _m[r, c];
      set => _m[r, c] = value;
    }

    public double[] MatrixAsVectorByRow => new[]
    {
      _m[0,0], _m[0,1], _m[0,2], _m[0,3],
      _m[1,0], _m[1,1], _m[1,2], _m[1,3],
      _m[2,0], _m[2,1], _m[2,2], _m[2,3],
      _m[3,0], _m[3,1], _m[3,2], _m[3,3]
    };

    public double[] MatrixAsVectorByColumn => new[]
    {
      _m[0,0], _m[1,0], _m[2,0], _m[3,0],
      _m[0,1], _m[1,1], _m[2,1], _m[3,1],
      _m[0,2], _m[1,2], _m[2,2], _m[3,2],
      _m[0,3], _m[1,3], _m[2,3], _m[3,3]
    };

    public float[] MatrixAsVectorFloatByColumn
    {
      get
      {
        var a = new float[16];
        int k = 0;
        for (int c = 0; c < 4; c++)
          for (int r = 0; r < 4; r++)
            a[k++] = (float)_m[r, c];
        return a;
      }
    }

    public float[,] GetFloatMatrix()
    {
      var r = new float[4, 4];
      for (int i = 0; i < 4; i++)
        for (int j = 0; j < 4; j++)
          r[i, j] = (float)_m[i, j];
      return r;
    }

    #endregion

    #region 기본 상태 설정

    public void Zero()
    {
      Array.Clear(_m, 0, _m.Length);
    }

    public void Identity()
    {
      Array.Clear(_m, 0, _m.Length);
      _m[0, 0] = 1.0; _m[1, 1] = 1.0; _m[2, 2] = 1.0; _m[3, 3] = 1.0;
    }

    public void Diagonal(double d)
    {
      Array.Clear(_m, 0, _m.Length);
      _m[0, 0] = d; _m[1, 1] = d; _m[2, 2] = d; _m[3, 3] = d;
    }

    #endregion

    #region 검사

    public bool IsIdentity(double tol)
    {
      return Math.Abs(1 - _m[0, 0]) <= tol && Math.Abs(_m[0, 1]) <= tol && Math.Abs(_m[0, 2]) <= tol && Math.Abs(_m[0, 3]) <= tol
          && Math.Abs(_m[1, 0]) <= tol && Math.Abs(1 - _m[1, 1]) <= tol && Math.Abs(_m[1, 2]) <= tol && Math.Abs(_m[1, 3]) <= tol
          && Math.Abs(_m[2, 0]) <= tol && Math.Abs(_m[2, 1]) <= tol && Math.Abs(1 - _m[2, 2]) <= tol && Math.Abs(_m[2, 3]) <= tol
          && Math.Abs(_m[3, 0]) <= tol && Math.Abs(_m[3, 1]) <= tol && Math.Abs(_m[3, 2]) <= tol && Math.Abs(1 - _m[3, 3]) <= tol;
    }

    public bool IsIdentity()
    {
      return _m[0, 0] == 1.0 && _m[0, 1] == 0.0 && _m[0, 2] == 0.0 && _m[0, 3] == 0.0
          && _m[1, 0] == 0.0 && _m[1, 1] == 1.0 && _m[1, 2] == 0.0 && _m[1, 3] == 0.0
          && _m[2, 0] == 0.0 && _m[2, 1] == 0.0 && _m[2, 2] == 1.0 && _m[2, 3] == 0.0
          && _m[3, 0] == 0.0 && _m[3, 1] == 0.0 && _m[3, 2] == 0.0 && _m[3, 3] == 1.0;
    }

    public bool IsTranslation =>
      _m[0, 0] == 1.0 && _m[1, 1] == 1.0 && _m[2, 2] == 1.0 && _m[3, 3] == 1.0 &&
      _m[0, 1] == 0.0 && _m[0, 2] == 0.0 && _m[1, 0] == 0.0 && _m[2, 0] == 0.0 &&
      _m[1, 2] == 0.0 && _m[2, 1] == 0.0 && _m[3, 0] == 0.0 && _m[3, 1] == 0.0 && _m[3, 2] == 0.0;

    public bool IsScaled
    {
      get
      {
        var cx = new Vector3D(_m[0, 0], _m[1, 0], _m[2, 0]);
        var cy = new Vector3D(_m[0, 1], _m[1, 1], _m[2, 1]);
        var cz = new Vector3D(_m[0, 2], _m[1, 2], _m[2, 2]);
        return Utility.Compare(1e-12, cx.LengthSquared, 1.0) != 0
            || Utility.Compare(1e-12, cy.LengthSquared, 1.0) != 0
            || Utility.Compare(1e-12, cz.LengthSquared, 1.0) != 0;
      }
    }

    public double ScaleFactorX
    {
      get { double a = _m[0, 0], b = _m[1, 0], c = _m[2, 0]; return Math.Sqrt(a * a + b * b + c * c); }
    }
    public double ScaleFactorY
    {
      get { double a = _m[0, 1], b = _m[1, 1], c = _m[2, 1]; return Math.Sqrt(a * a + b * b + c * c); }
    }
    public double ScaleFactorZ
    {
      get { double a = _m[0, 2], b = _m[1, 2], c = _m[2, 2]; return Math.Sqrt(a * a + b * b + c * c); }
    }

    public bool EqualScaleFactors()
    {
      double sx = ScaleFactorX, sy = ScaleFactorY;
      if (!Utility.AreEqual(sx, sy, Math.Max(Math.Abs(sx), Math.Abs(sy)))) return false;
      double sz = ScaleFactorZ;
      return Utility.AreEqual(sy, sz, Math.Max(Math.Abs(sy), Math.Abs(sz)));
    }

    public bool IsScaleFactorUniform()
    {
      double sx = Math.Abs(ScaleFactorX), sy = Math.Abs(ScaleFactorY), sz = Math.Abs(ScaleFactorZ);
      return Math.Abs(sx - sy) < Utility.TOL9 && Math.Abs(sy - sz) < Utility.TOL9;
    }

    public bool IsScaleFactorUniformForPlanar(Plane pl, ref double scaleFactor)
    {
      double sx = Math.Abs(ScaleFactorX), sy = Math.Abs(ScaleFactorY), sz = Math.Abs(ScaleFactorZ);
      if (Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisZ)) return Math.Abs(sx - sy) < Utility.TOL9;
      if (Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisY)) return Math.Abs(sx - sz) < Utility.TOL9;
      if (!Vector3D.AreParallel(pl.AxisZ, Vector3D.AxisX)) return false;
      scaleFactor = ScaleFactorY;
      return Math.Abs(sz - sy) < Utility.TOL9;
    }

    public bool IsReversed() => devDept.Geometry.Matrix.Determinant3(Matrix) < 0.0;

    #endregion

    #region 빌더(스케일/이동/투영)

    public void Scaling(double sx, double sy, double sz = 1.0)
    {
      Array.Clear(_m, 0, _m.Length);
      _m[0, 0] = sx; _m[1, 1] = sy; _m[2, 2] = sz; _m[3, 3] = 1.0;
    }

    public void Scaling(Vector3D s)
    {
      Array.Clear(_m, 0, _m.Length);
      _m[0, 0] = s.X; _m[1, 1] = s.Y; _m[2, 2] = s.Z; _m[3, 3] = 1.0;
    }

    public void Scaling(Point3D fixedPoint, double uniformScale)
    {
      if (fixedPoint.X == 0 && fixedPoint.Y == 0 && fixedPoint.Z == 0)
      {
        Scaling(uniformScale, uniformScale, uniformScale);
        return;
      }
      var t1 = new Transformation(); t1.Translation(Point3D.Origin.X - fixedPoint.X, Point3D.Origin.Y - fixedPoint.Y, Point3D.Origin.Z - fixedPoint.Z);
      var s  = new Transformation(); s.Scaling(uniformScale, uniformScale, uniformScale);
      var t2 = new Transformation(); t2.Translation(fixedPoint.X - Point3D.Origin.X, fixedPoint.Y - Point3D.Origin.Y, fixedPoint.Z - Point3D.Origin.Z);
      _m = (t2 * s * t1)._m;
    }

    public void Scaling(Point3D fixedPoint, double sx, double sy, double sz = 1.0)
    {
      if (fixedPoint.X == 0 && fixedPoint.Y == 0 && fixedPoint.Z == 0)
      {
        Scaling(sx, sy, sz);
        return;
      }
      var t1 = new Transformation(); t1.Translation(Point3D.Origin.X - fixedPoint.X, Point3D.Origin.Y - fixedPoint.Y, Point3D.Origin.Z - fixedPoint.Z);
      var s  = new Transformation(); s.Scaling(sx, sy, sz);
      var t2 = new Transformation(); t2.Translation(fixedPoint.X - Point3D.Origin.X, fixedPoint.Y - Point3D.Origin.Y, fixedPoint.Z - Point3D.Origin.Z);
      _m = (t2 * s * t1)._m;
    }

    public void Translation(double dx, double dy, double dz = 0.0)
    {
      Identity();
      _m[0, 3] = dx; _m[1, 3] = dy; _m[2, 3] = dz;
    }

    public void Translation(Vector3D v) => Translation(v.X, v.Y, v.Z);

    /// <summary>평면으로의 정사영 행렬 (축: plane.X/Y, 원점 보정)</summary>
    public void PlanarProjection(Plane plane)
    {
      var X = new[] { plane.AxisX.X, plane.AxisX.Y, plane.AxisX.Z };
      var Y = new[] { plane.AxisY.X, plane.AxisY.Y, plane.AxisY.Z };
      var O = new[] { plane.Origin.X, plane.Origin.Y, plane.Origin.Z };

      for (int r = 0; r < 3; r++)
      {
        for (int c = 0; c < 3; c++)
          _m[r, c] = X[r] * X[c] + Y[r] * Y[c];

        double t = _m[r, 0] * O[0] + _m[r, 1] * O[1] + _m[r, 2] * O[2];
        _m[r, 3] = O[r] - t;
      }
      _m[3, 0] = _m[3, 1] = _m[3, 2] = 0.0; _m[3, 3] = 1.0;
    }

    #endregion

    #region 선형 연산

    public void Transpose()
    {
      void Swap(int r1, int c1, int r2, int c2)
      { double t = _m[r1, c1]; _m[r1, c1] = _m[r2, c2]; _m[r2, c2] = t; }

      Swap(0, 1, 1, 0);
      Swap(0, 2, 2, 0);
      Swap(0, 3, 3, 0);
      Swap(1, 2, 2, 1);
      Swap(1, 3, 3, 1);
      Swap(2, 3, 3, 2);
    }

    public void Invert()
    {
      var copy = new double[4, 4];
      Array.Copy(_m, copy, 16);
      _m = devDept.Geometry.Matrix.Inverse4(copy);
    }

    public static Transformation operator *(Transformation a, Transformation b)
    {
      var r = new Transformation();
      var m = r._m;
      for (int i = 0; i < 4; i++)
      {
        for (int j = 0; j < 4; j++)
        {
          m[i, j] = a._m[i, 0] * b._m[0, j] + a._m[i, 1] * b._m[1, j] + a._m[i, 2] * b._m[2, j] + a._m[i, 3] * b._m[3, j];
        }
      }
      return r;
    }

    public static Transformation operator +(Transformation a, Transformation b)
    {
      var r = new Transformation();
      for (int i = 0; i < 4; i++)
        for (int j = 0; j < 4; j++)
          r._m[i, j] = a._m[i, j] + b._m[i, j];
      return r;
    }

    public static Transformation operator -(Transformation a, Transformation b)
    {
      var r = new Transformation();
      for (int i = 0; i < 4; i++)
        for (int j = 0; j < 4; j++)
          r._m[i, j] = a._m[i, j] - b._m[i, j];
      return r;
    }

    #endregion

    #region 벡터/포인트 변환 (좌측 곱)

    public double[] ActOnLeft(double x, double y, double z, double w)
    {
      return new[]
      {
        _m[0,0]*x + _m[0,1]*y + _m[0,2]*z + _m[0,3]*w,
        _m[1,0]*x + _m[1,1]*y + _m[1,2]*z + _m[1,3]*w,
        _m[2,0]*x + _m[2,1]*y + _m[2,2]*z + _m[2,3]*w,
        _m[3,0]*x + _m[3,1]*y + _m[3,2]*z + _m[3,3]*w
      };
    }

    public double[] ActOnLeftOne(double x, double y, double z)
    {
      return new[]
      {
        _m[0,0]*x + _m[0,1]*y + _m[0,2]*z + _m[0,3],
        _m[1,0]*x + _m[1,1]*y + _m[1,2]*z + _m[1,3],
        _m[2,0]*x + _m[2,1]*y + _m[2,2]*z + _m[2,3]
      };
    }

    public double[] ActOnLeftZero(double x, double y, double z)
    {
      return new[]
      {
        _m[0,0]*x + _m[0,1]*y + _m[0,2]*z,
        _m[1,0]*x + _m[1,1]*y + _m[1,2]*z,
        _m[2,0]*x + _m[2,1]*y + _m[2,2]*z
      };
    }

    // 필요 시(정적 헬퍼 유지)
    public static float[] ActOnLeftOne(float x, float y, float z, float[,] m)
    {
      return new[]
      {
        (float)(m[0,0]*x + m[0,1]*y + m[0,2]*z) + m[0,3],
        (float)(m[1,0]*x + m[1,1]*y + m[1,2]*z) + m[1,3],
        (float)(m[2,0]*x + m[2,1]*y + m[2,2]*z) + m[2,3],
      };
    }

    public static float[] ActOnLeftZero(float x, float y, float z, float[,] m)
    {
      return new[]
      {
        (float)(m[0,0]*x + m[0,1]*y + m[0,2]*z),
        (float)(m[1,0]*x + m[1,1]*y + m[1,2]*z),
        (float)(m[2,0]*x + m[2,1]*y + m[2,2]*z),
      };
    }

    public static Point2D operator *(Transformation t, Point2D p)
    {
      var h = t.ActOnLeft(p.X, p.Y, 0.0, 1.0);
      double invW = (h[3] != 0.0) ? 1.0 / h[3] : 1.0;
      return new Point2D(invW * h[0], invW * h[1]);
    }

    public static Point3D operator *(Transformation t, Point3D p)
    {
      var h = t.ActOnLeft(p.X, p.Y, p.Z, 1.0);
      double invW = (h[3] != 0.0) ? 1.0 / h[3] : 1.0;
      return new Point3D(invW * h[0], invW * h[1], invW * h[2]);
    }

    public static PointRGB operator *(Transformation t, PointRGB p)
    {
      var h = t.ActOnLeft(p.X, p.Y, p.Z, 1.0);
      double invW = (h[3] != 0.0) ? 1.0 / h[3] : 1.0;
      return new PointRGB(invW * h[0], invW * h[1], invW * h[2], p.R, p.G, p.B);
    }

    public static Point4D operator *(Transformation t, Point4D h)
    {
      var r = t.ActOnLeft(h.X, h.Y, h.Z, h.W);
      return new Point4D(r[0], r[1], r[2], r[3]);
    }

    public static Vector2D operator *(Transformation t, Vector2D v)
    {
      var h = t.ActOnLeft(v.X, v.Y, 0.0, 0.0);
      double invW = (h[3] != 0.0) ? 1.0 / h[3] : 1.0;
      return new Vector2D(invW * h[0], invW * h[1]);
    }

    public static Vector3D operator *(Transformation t, Vector3D v)
    {
      var h = t.ActOnLeft(v.X, v.Y, v.Z, 0.0);
      double invW = (h[3] != 0.0) ? 1.0 / h[3] : 1.0;
      return new Vector3D(invW * h[0], invW * h[1], invW * h[2]);
    }

    #endregion

    #region 회전/미러/기저변환

    public void Rotation(double angleRad, Vector3D axis)
      => UpdateMatrix(Math.Sin(angleRad), Math.Cos(angleRad), axis, Point3D.Origin);

    public void Rotation(double angleRad, Point3D axisStart, Point3D axisEnd)
      => UpdateMatrix(Math.Sin(angleRad), Math.Cos(angleRad), Vector3D.Subtract(axisEnd, axisStart), axisStart);

    public void Rotation(double angleRad, Vector3D axis, Point3D center)
      => UpdateMatrix(Math.Sin(angleRad), Math.Cos(angleRad), axis, center);

    /// <summary>startDir → endDir 회전(라디안), 중심 center</summary>
    public void Rotation(Vector3D startDir, Vector3D endDir, Point3D center)
    {
      var u = (Vector3D)startDir.Clone(); u.Normalize();
      var v = (Vector3D)endDir.Clone();   v.Normalize();

      double cos = u * v;
      var w = Vector3D.Cross(u, v);
      double s = w.Length;
      w.Normalize();

      if (s == 0.0)
      {
        // u, v가 같은/반대 방향
        w.PerpendicularTo(u); w.Normalize();
        s = 0.0;
        cos = (cos < 0.0) ? -1.0 : 1.0;
      }
      UpdateMatrix(s, cos, w, center);
    }

    internal void UpdateMatrix(double sinTheta, double cosTheta, Vector3D axis, Point3D center)
    {
      Identity();

      // (sin, cos) 정규화
      double l2 = sinTheta * sinTheta + cosTheta * cosTheta;
      if (Math.Abs(l2 - 1.0) > 1.490116119385E-08)
      {
        var s = new Vector2D(cosTheta, sinTheta); s.Normalize();
        cosTheta = s.X; sinTheta = s.Y;
      }
      if (Math.Abs(sinTheta) >= 0.9999999850988388 && Math.Abs(cosTheta) <= 1.490116119385E-08)
      { cosTheta = 0.0; sinTheta = Math.Sign(sinTheta); }
      else if (Math.Abs(cosTheta) >= 0.9999999850988388 && Math.Abs(sinTheta) <= 1.490116119385E-08)
      { sinTheta = 0.0; cosTheta = Math.Sign(cosTheta); }

      if (sinTheta == 0.0 && cosTheta == 1.0) return; // 0회전

      var n = (Vector3D)axis.Clone();
      if (Math.Abs(n.LengthSquared - 1.0) > 2.220446049250313E-16) n.Normalize();

      double c = cosTheta, s = sinTheta, d = 1.0 - c;
      double nx = n.X, ny = n.Y, nz = n.Z;

      _m[0, 0] = nx * nx * d + c;
      _m[0, 1] = nx * ny * d - nz * s;
      _m[0, 2] = nx * nz * d + ny * s;

      _m[1, 0] = ny * nx * d + nz * s;
      _m[1, 1] = ny * ny * d + c;
      _m[1, 2] = ny * nz * d - nx * s;

      _m[2, 0] = nz * nx * d - ny * s;
      _m[2, 1] = nz * ny * d + nx * s;
      _m[2, 2] = nz * nz * d + c;

      if (center.X != 0.0 || center.Y != 0.0 || center.Z != 0.0)
      {
        _m[0, 3] = -((_m[0, 0] - 1.0) * center.X + _m[0, 1] * center.Y + _m[0, 2] * center.Z);
        _m[1, 3] = -(_m[1, 0] * center.X + (_m[1, 1] - 1.0) * center.Y + _m[1, 2] * center.Z);
        _m[2, 3] = -(_m[2, 0] * center.X + _m[2, 1] * center.Y + (_m[2, 2] - 1.0) * center.Z);
      }
      _m[3, 0] = _m[3, 1] = _m[3, 2] = 0.0; _m[3, 3] = 1.0;
    }

    public void Rotation(Vector3D X0, Vector3D Y0, Vector3D Z0, Vector3D X1, Vector3D Y1, Vector3D Z1)
    {
      var to   = new Transformation { [0,0]=X1.X, [0,1]=Y1.X, [0,2]=Z1.X, [1,0]=X1.Y, [1,1]=Y1.Y, [1,2]=Z1.Y, [2,0]=X1.Z, [2,1]=Y1.Z, [2,2]=Z1.Z, [3,3]=1.0 };
      var from = new Transformation { [0,0]=X0.X, [0,1]=X0.Y, [0,2]=X0.Z, [1,0]=Y0.X, [1,1]=Y0.Y, [1,2]=Y0.Z, [2,0]=Z0.X, [2,1]=Z0.Y, [2,2]=Z0.Z, [3,3]=1.0 };
      _m = (to * from)._m;
    }

    public void Rotation(Plane plane0, Plane plane1)
    {
      Rotation(plane0.Origin, plane0.AxisX, plane0.AxisY, plane0.AxisZ, plane1.Origin, plane1.AxisX, plane1.AxisY, plane1.AxisZ);
    }

    public void Rotation(Point3D P0, Vector3D X0, Vector3D Y0, Vector3D Z0, Point3D P1, Vector3D X1, Vector3D Y1, Vector3D Z1)
    {
      var t1 = new Transformation(); t1.Translation(-P0.X, -P0.Y, -P0.Z);
      var r  = new Transformation(); r.Rotation(X0, Y0, Z0, X1, Y1, Z1);
      var t2 = new Transformation(); t2.Translation(P1.X, P1.Y, P1.Z);
      _m = (t2 * r * t1)._m;
    }

    public void Mirror(Plane plane) => Mirror(plane.Origin, plane.AxisZ);

    public void Mirror(Point3D pointOnPlane, Vector3D normal)
    {
      var n = (Vector3D)normal.Clone(); n.Normalize();
      var twoPn = 2.0 * (n.X * pointOnPlane.X + n.Y * pointOnPlane.Y + n.Z * pointOnPlane.Z) * n;

      _m[0, 0] = 1.0 - 2.0 * n.X * n.X; _m[0, 1] = -2.0 * n.X * n.Y;     _m[0, 2] = -2.0 * n.X * n.Z;     _m[0, 3] = twoPn.X;
      _m[1, 0] = -2.0 * n.Y * n.X;     _m[1, 1] = 1.0 - 2.0 * n.Y * n.Y; _m[1, 2] = -2.0 * n.Y * n.Z;     _m[1, 3] = twoPn.Y;
      _m[2, 0] = -2.0 * n.Z * n.X;     _m[2, 1] = -2.0 * n.Z * n.Y;     _m[2, 2] = 1.0 - 2.0 * n.Z * n.Z; _m[2, 3] = twoPn.Z;
      _m[3, 0] = 0.0; _m[3, 1] = 0.0; _m[3, 2] = 0.0; _m[3, 3] = 1.0;
    }

    public bool ChangeBasis(Plane initial, Plane final)
      => ChangeBasis(initial.Origin, initial.AxisX, initial.AxisY, initial.AxisZ, final.Origin, final.AxisX, final.AxisY, final.AxisZ);

    public bool ChangeBasis(Vector3D X0, Vector3D Y0, Vector3D Z0, Vector3D X1, Vector3D Y1, Vector3D Z1)
    {
      Zero(); _m[3, 3] = 1.0;

      double x1y1 = X1 * Y1, x1z1 = X1 * Z1, y1z1 = Y1 * Z1;

      double[,] A = new double[3, 6]
      {
        { X1*X1, x1y1,  x1z1,  X1*X0, X1*Y0, X1*Z0 },
        { x1y1,  Y1*Y1, y1z1,  Y1*X0, Y1*Y0, Y1*Z0 },
        { x1z1,  y1z1,  Z1*Z1, Z1*X0, Z1*Y0, Z1*Z0 }
      };

      int p = (A[0, 0] >= A[1, 1]) ? 0 : 1; if (A[2, 2] > A[p, p]) p = 2;
      int q = (p + 1) % 3; int r = (q + 1) % 3;

      if (A[p, p] == 0.0) return false;

      double inv = 1.0 / A[p, p];
      for (int j = 0; j < 6; j++) A[p, j] *= inv; A[p, p] = 1.0;

      if (A[q, p] != 0.0)
      {
        double f = -A[q, p];
        for (int j = 0; j < 6; j++) A[q, j] += f * A[p, j];
        A[q, p] = 0.0;
      }
      if (A[r, p] != 0.0)
      {
        double f = -A[r, p];
        for (int j = 0; j < 6; j++) A[r, j] += f * A[p, j];
        A[r, p] = 0.0;
      }

      if (Math.Abs(A[q, q]) < Math.Abs(A[r, r])) { int tmp = q; q = r; r = tmp; }

      if (A[q, q] == 0.0) return false;

      inv = 1.0 / A[q, q];
      for (int j = 0; j < 6; j++) A[q, j] *= inv; A[q, q] = 1.0;

      if (A[p, q] != 0.0)
      {
        double f = -A[p, q];
        for (int j = 0; j < 6; j++) A[p, j] += f * A[q, j];
        A[p, q] = 0.0;
      }
      if (A[r, q] != 0.0)
      {
        double f = -A[r, q];
        for (int j = 0; j < 6; j++) A[r, j] += f * A[q, j];
        A[r, q] = 0.0;
      }

      if (A[r, r] == 0.0) return false;

      inv = 1.0 / A[r, r];
      for (int j = 0; j < 6; j++) A[r, j] *= inv; A[r, r] = 1.0;

      if (A[p, r] != 0.0)
      {
        double f = -A[p, r];
        for (int j = 0; j < 6; j++) A[p, j] += f * A[r, j];
        A[p, r] = 0.0;
      }
      if (A[q, r] != 0.0)
      {
        double f = -A[q, r];
        for (int j = 0; j < 6; j++) A[q, j] += f * A[r, j];
        A[q, r] = 0.0;
      }

      _m[0, 0] = A[0, 3]; _m[0, 1] = A[0, 4]; _m[0, 2] = A[0, 5];
      _m[1, 0] = A[1, 3]; _m[1, 1] = A[1, 4]; _m[1, 2] = A[1, 5];
      _m[2, 0] = A[2, 3]; _m[2, 1] = A[2, 4]; _m[2, 2] = A[2, 5];

      return true;
    }

    public bool ChangeBasis(Point3D P0, Vector3D X0, Vector3D Y0, Vector3D Z0, Point3D P1, Vector3D X1, Vector3D Y1, Vector3D Z1)
    {
      var from = new Transformation(P0, X0, Y0, Z0);
      var t    = new Transformation(); t.Translation(-P1.X, -P1.Y, -P1.Z);
      var rot  = new Transformation();
      bool ok  = rot.ChangeBasis(Vector3D.AxisX, Vector3D.AxisY, Vector3D.AxisZ, X1, Y1, Z1);
      _m = (rot * t * from)._m;
      return ok;
    }

    public static void AutocadOCS(Vector3D normal, out Vector3D xAxis, out Vector3D yAxis)
    {
      var n = (Vector3D)normal.Clone(); n.Normalize();
      var aY = new Vector3D(0, 1, 0);
      var aZ = new Vector3D(0, 0, 1);
      xAxis = (Math.Abs(n.X) >= 1.0 / 64.0 || Math.Abs(n.Y) >= 1.0 / 64.0) ? Vector3D.Cross(aZ, n) : Vector3D.Cross(aY, n);
      xAxis.Normalize();
      yAxis = Vector3D.Cross(n, xAxis); yAxis.Normalize();
    }

    #endregion

    #region 유틸

    public string Dump()
    {
      var sb = new StringBuilder();
      for (int r = 0; r < 4; r++)
      {
        sb.AppendFormat("{0}, {1}, {2}, {3}", _m[r, 0], _m[r, 1], _m[r, 2], _m[r, 3]);
        if (r < 3) sb.Append(Environment.NewLine);
      }
      return sb.ToString();
    }

    public Transformation GetTransformationForNormals()
    {
      var n = new Transformation(this);
      n._m[0, 3] = n._m[1, 3] = n._m[2, 3] = 0.0; // 평행이동 제거
      n.Invert();
      n.Transpose(); // (M⁻¹)ᵀ
      return n;
    }

    #endregion
  }
}


```
