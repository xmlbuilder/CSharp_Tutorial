## Plane 
```csharp
namespace Geometry
{

  public enum planeIntersectionType
  {
    Disjoint,
    Coincide,
    UniqueLine,
  }

  [Serializable]
  public class Plane : ICloneable
  {
    private Point3D origin;
    private Vector3D xAxis;
    private Vector3D yAxis;
    private Vector3D zAxis;
    private PlaneEquation equation = new PlaneEquation();

    public Plane()
    {
      this.origin = Point3D.Origin;
      this.xAxis = Vector3D.AxisX;
      this.yAxis = Vector3D.AxisY;
      this.zAxis = Vector3D.AxisZ;
      this.equation.X = this.equation.Y = this.equation.D = 0.0;
      this.equation.Z = 1.0;
    }

    public Plane(Point3D P, Vector3D N)
    {
      if (!this.SetOriginNormal((Point3D) P.Clone(), (Vector3D) N.Clone()))
        throw new ArgumentException("Invalid Plane");
    }

    public Plane(Vector3D N)
    {
      if (!this.SetOriginNormal(Point3D.Origin, (Vector3D) N.Clone()))
        throw new ArgumentException("Invalid Plane");
    }

    public Plane(Point3D P, Vector3D X, Vector3D Y)
    {
      if (!this.SetOriginXYVector((Point3D) P.Clone(), (Vector3D) X.Clone(), Y))
        throw new ArgumentException("Invalid Plane");
    }

    protected Plane(Plane another)
    {
      this.SetOriginXYVector((Point3D) another.origin.Clone(), (Vector3D) another.xAxis.Clone(), another.yAxis);
    }

    public Plane(Point3D P, Point3D Q, Point3D R) => this.CreateFromPoints(P, Q, R);

    public Plane(double[] e)
    {
      if (!this.SetPlaneEquation(e))
        throw new ArgumentException("Invalid Plane");
    }

    public virtual object Clone() => (object) new Plane(this);

    internal bool SetOriginNormal(Point3D origin, Vector3D normal)
    {
        this.origin = origin;
        this.zAxis  = normal;

        // z축(법선) 정규화 성공 여부
        bool ok = this.zAxis.Normalize();

        // x축은 기본 X에서 z축에 직교하도록 조정 → 정규화
        this.xAxis = Vector3D.AxisX;
        this.xAxis.PerpendicularTo(this.zAxis);
        this.xAxis.Normalize();

        // y축 = z × x (오른손 좌표계), 정규화
        this.yAxis = Vector3D.Cross(this.zAxis, this.xAxis);
        this.yAxis.Normalize();

        // 평면 방정식 갱신
        this.UpdateEquation();

        return ok;
    }

    private bool SetOriginXYVector(Point3D origin, Vector3D xHint, Vector3D yHint)
    {
        this.origin = origin;

        // x축: 힌트를 정규화
        this.xAxis = xHint;
        this.xAxis.Normalize();

        // y축: x축에 직교 성분만 추출 후 정규화
        this.yAxis = yHint - Vector3D.Dot(yHint, this.xAxis) * this.xAxis;
        this.yAxis.Normalize();

        // z축 = x × y (오른손 좌표계)
        this.zAxis = Vector3D.Cross(this.xAxis, this.yAxis);

        // z축 정규화 성공 여부
        bool ok = this.zAxis.Normalize();

        // 평면 방정식 갱신
        this.UpdateEquation();

        return ok;
    }

    public bool CreateFromPoints(Point3D P, Point3D Q, Point3D R)
    {
      this.origin = P;
      this.zAxis = new Vector3D(P, Q, R);
      bool fromPoints = !this.zAxis.IsZero;
      this.xAxis = Vector3D.Subtract(Q, P);
      this.xAxis.Normalize();
      this.yAxis = Vector3D.Cross(this.zAxis, this.xAxis);
      this.yAxis.Normalize();
      if (!this.equation.Create(this.origin, this.zAxis))
        fromPoints = false;
      return fromPoints;
    }

    // coeffs = [A, B, C, D] for plane Ax + By + Cz + D = 0
    private bool SetPlaneEquation(double[] coeffs)
    {
        bool ok = false;

        // 1) 평면 방정식 계수 저장
        this.equation = new PlaneEquation
        {
            X = coeffs[0],
            Y = coeffs[1],
            Z = coeffs[2],
            D = coeffs[3],
        };

        // 2) 법선(zAxis)과 원점에서 평면까지의 최근접점(origin)
        this.zAxis = new Vector3D(coeffs[0], coeffs[1], coeffs[2]); // n = (A,B,C)
        double nLen = this.zAxis.Length;
        this.zAxis.Normalize();

        if (nLen > 0.0)
        {
            // origin = -D * n / |n|^2  (여기서는 n을 단위화한 뒤 -D/|n| 배)
            this.origin = -(1.0 / nLen) * this.equation.D * this.zAxis.AsPoint;
            ok = true;
        }

        // 3) 직교 기저 구성: x ⟂ z, y = z × x (오른손좌표계)
        this.xAxis = new Vector3D();
        this.xAxis.PerpendicularTo(this.zAxis);
        this.xAxis.Normalize();

        this.yAxis = Vector3D.Cross(this.zAxis, this.xAxis);
        this.yAxis.Normalize();

        return ok;
    }


    public void UpdateEquation() => this.equation = new PlaneEquation(this.origin, this.zAxis);

    public Point3D Reflect(Point3D point)
    {
      double dist = this.DistanceTo(point);
      return new Point3D((this.PointAt(this.Project(point)) - dist * this.zAxis).ToArray());
    }

    public Vector3D Reflect(Vector3D vector)
    {
      return new Vector3D(new Plane(this.zAxis).Reflect(new Point3D(vector.ToArray())).ToArray());
    }

    public bool IsValid()
    {
        // 1) 평면 방정식 자체 유효성
        if (!this.equation.IsValid())
            return false;

        // 2) 원점(origin)이 평면 방정식을 충분히 만족하는지(수치 허용오차 포함)
        const double strictTol = 1e-12;
        const double ulp = 2.220446049250313E-15; // DBL_EPSILON

        double valueAtOrigin = this.equation.ValueAt(this.origin); // Ax0 + By0 + Cz0 + D
        if (Math.Abs(valueAtOrigin) > strictTol)
        {
            // 스케일에 따른 허용 오차 확장
            double scale = Math.Abs(this.origin.MaximumCoordinate) + Math.Abs(this.equation.D);

            // 너무 작은 스케일이거나 origin 자체가 무효면 실패
            if (scale <= 1000.0 || !this.origin.IsValid())
            return false;

            double scaledTol = scale * ulp; // 스케일 비례 허용오차
            if (Math.Abs(valueAtOrigin) > scaledTol)
            return false;
        }

        // 3) 보유 중인 x/y/z 축이 일관되게 설정되어 있는지 확인
        if (!this.SetXYZAxis(this.xAxis, this.yAxis, this.zAxis))
            return false;

        // 4) 방정식의 법선과 z축 정렬(단위 벡터 내적이 1에 가까운지)
        const double unitTol = 1.490116119385E-08;
        Vector3D eqNormal = new Vector3D(this.equation.X, this.equation.Y, this.equation.Z);
        eqNormal.Normalize();

        return Math.Abs(eqNormal * this.zAxis - 1.0) <= unitTol;
    }

    private bool ChecKXYZValidate(
        Vector3D x,
        Vector3D y,
        Vector3D z)
    {
        // 유효성/영벡터 방어
        if (!x.IsValid() || !y.IsValid() || !z.IsValid())
            return false;

        const double tiny = 1.490116119385E-08;   // 엄격 허용오차
        const double loose = 1.52587890625E-05;   // 느슨한 허용오차(교차검증용)

        double lenX = x.Length;
        double lenY = y.Length;
        double lenZ = z.Length;
        if (lenX <= tiny || lenY <= tiny || lenZ <= tiny)
            return false;

        double invX = 1.0 / lenX;
        double invY = 1.0 / lenY;
        double invZ = 1.0 / lenZ;

        // 정규화된 코사인(직교성 검사)
        double cosXY = (x.X * y.X + x.Y * y.Y + x.Z * y.Z) * invX * invY;
        double cosYZ = (y.X * z.X + y.Y * z.Y + y.Z * z.Z) * invY * invZ;
        double cosZX = (z.X * x.X + z.Y * x.Y + z.Z * x.Z) * invZ * invX;

        // 엄격 허용오차를 넘는 비직교성이면 추가 교차검증
        if (Math.Abs(cosXY) > tiny || Math.Abs(cosYZ) > tiny || Math.Abs(cosZX) > tiny)
        {
            // 느슨한 허용오차도 넘으면 실패
            if (Math.Abs(cosXY) >= loose || Math.Abs(cosYZ) >= loose || Math.Abs(cosZX) >= loose)
            return false;

            // 교차검증: (x×y)·z ≈ |x||y||z|
            Vector3D nXY = invX * invY * Vector3D.Cross(x, y);
            if (Math.Abs(Math.Abs((nXY.X * z.X + nXY.Y * z.Y + nXY.Z * z.Z) * invZ) - 1.0) > tiny)
            return false;

            Vector3D nYZ = invY * invZ * Vector3D.Cross(y, z);
            if (Math.Abs(Math.Abs((nYZ.X * x.X + nYZ.Y * x.Y + nYZ.Z * x.Z) * invX) - 1.0) > tiny)
            return false;

            Vector3D nZX = invZ * invX * Vector3D.Cross(z, x);
            if (Math.Abs(Math.Abs((nZX.X * y.X + nZX.Y * y.Y + nZX.Z * y.Z) * invY) - 1.0) > tiny)
            return false;
        }

        return true;
    }

    private bool ChecKXYZAxis(
        Vector3D x,
        Vector3D y,
        Vector3D z)
    {
        const double unitTol = 1.490116119385E-08;
        return
            this.ChecKXYZValidate(x, y, z) &&
            Math.Abs(x.Length - 1.0) <= unitTol &&
            Math.Abs(y.Length - 1.0) <= unitTol &&
            Math.Abs(z.Length - 1.0) <= unitTol;
    }


    private bool SetXYZAxis(Vector3D xAxis, Vector3D yAxis, Vector3D zAxis)
    {
        // 축의 유효성/단위/직교성 검사 (기존 로직 유지)
        if (!this.ChecKXYZAxis(xAxis, yAxis, zAxis))
            return false;

        // 오른손 좌표계 여부 검사: 삼중곱 (x × y) · z > tol
        const double rightHandTol = 1.490116119385E-08;
        double triple = Vector3D.Cross(xAxis, yAxis) * zAxis;

        return triple > rightHandTol;
    }


    public Point3D PointAt(Point2D pt) => this.PointAt(pt.X, pt.Y);

    public Point3D PointAt(double s, double t) => this.origin + s * this.xAxis + t * this.yAxis;

    public Point3D PointAt(double s, double t, double c)
    {
      return this.origin + s * this.xAxis + t * this.yAxis + c * this.zAxis;
    }

    public static planeIntersectionType Intersection(Plane pln1, Plane pln2, out Segment3D intSeg)
    {
      Point3D pt;
      Vector3D u;
      int num = (int) Plane.Intersection(pln1, pln2, 1E-12, out pt, out u);
      intSeg = new Segment3D(pt, pt + u);
      return (planeIntersectionType) num;
    }

    public static planeIntersectionType Intersection(
      Plane pln1,
      Plane pln2,
      double tol,
      out Segment3D intSeg)
    {
      Point3D pt;
      Vector3D u;
      int num = (int) Plane.Intersection(pln1, pln2, tol, out pt, out u);
      intSeg = new Segment3D(pt, pt + u);
      return (planeIntersectionType) num;
    }

    public static planeIntersectionType Intersection(
    Plane    plnA,
    Plane    plnB,
    double   tol,
    out Point3D pointOnLine,
    out Vector3D dir)
    {
        pointOnLine = new Point3D();

        // 교선 방향 = 법선의 외적
        Vector3D nA = plnA.AxisZ;
        Vector3D nB = plnB.AxisZ;
        dir = Vector3D.Cross(nA, nB);

        // 평행/동일면 판정: |dir|≈0 이면 두 평면이 평행
        const double parallelTol = 1e-9;
        double absSum = Math.Abs(dir.X) + Math.Abs(dir.Y) + Math.Abs(dir.Z);
        if (absSum < parallelTol)
        {
            // 원점 차 벡터가 plnA의 법선에 수직이면 동일면, 아니면 서로 다른 평행면
            Vector3D dO = Vector3D.Subtract(plnB.Origin, plnA.Origin);
            return (Utility.Compare(tol, nA * dO, 0.0) == 0)
            ? planeIntersectionType.Coincide
            : planeIntersectionType.Disjoint;
        }

        // 교선 위 한 점 구하기: dir의 지배 성분을 기준으로 x/y/z 중 하나를 0으로 두고 연립 방정식 해
        double ax = Math.Abs(dir.X), ay = Math.Abs(dir.Y), az = Math.Abs(dir.Z);
        // dominant: 1 -> X, 2 -> Y, 3 -> Z
        int dominant = (ax <= ay) ? ((ay <= az) ? 3 : 2) : ((ax <= az) ? 3 : 1);

        // 평면 방정식: n·X + d = 0 에서 d = -n·origin
        double dA = -Vector3D.Dot(nA, plnA.Origin);
        double dB = -Vector3D.Dot(nB, plnB.Origin);

        switch (dominant)
        {
            case 1: // X가 가장 큼: x=0으로 두고 y,z 해
            pointOnLine.X = 0.0;
            pointOnLine.Y = (dB * nA.Z - dA * nB.Z) / dir.X;
            pointOnLine.Z = (dA * nB.Y - dB * nA.Y) / dir.X;
            break;

            case 2: // Y가 가장 큼: y=0
            pointOnLine.X = (dA * nB.Z - dB * nA.Z) / dir.Y;
            pointOnLine.Y = 0.0;
            pointOnLine.Z = (dB * nA.X - dA * nB.X) / dir.Y;
            break;

            default: // 3: Z가 가장 큼: z=0
            pointOnLine.X = (dB * nA.Y - dA * nB.Y) / dir.Z;
            pointOnLine.Y = (dA * nB.X - dB * nA.X) / dir.Z;
            pointOnLine.Z = 0.0;
            break;
        }

        return planeIntersectionType.UniqueLine;
    }


    public void TransformBy(Transformation xform)
    {
      Point3D b = xform * this.origin;
      Vector3D vector3D1 = Vector3D.Subtract(xform * (this.origin + this.xAxis), b);
      Vector3D vector3D2 = Vector3D.Subtract(xform * (this.origin + this.yAxis), b);
      this.SetOriginXYVector(b, vector3D1, vector3D2);
    }

    public Plane Offset(double amount)
    {
      Plane plane = new Plane(this);
      plane.Origin += this.zAxis * amount;
      return plane;
    }

    internal void Transform(double sinAngle, double cosAngle, Vector3D axis)
    {
        // 회전축이 현재 z축과 거의 동일하면: x/y 축만 2×2 회전으로 빠르게 갱신
        if (Vector3D.AreCoincident(axis, this.zAxis))
        {
            // [x'; y'] = [ [c  s], [-s  c] ] [x; y]
            Vector3D newX =  cosAngle * this.xAxis + sinAngle * this.yAxis;
            Vector3D newY =  cosAngle * this.yAxis - sinAngle * this.xAxis;

            this.xAxis = newX;
            this.yAxis = newY;
        }
        else
        {
            // 일반 케이스: 원점은 그대로 두고 축들만 회전
            Point3D savedOrigin = this.origin;
            this.Transform(sinAngle, cosAngle, axis, this.origin);
            this.origin = savedOrigin;
        }
    }

    internal void Transform(double sinAngle, double cosAngle, Vector3D axis, Point3D pivot)
    {
        Transformation xform = new Transformation();

        if (pivot == this.origin)
        {
            // 평면의 origin은 고정하고, 축만 회전시키기 위해
            // 회전 중심을 (월드) 원점으로 두고 축에만 적용
            xform.UpdateMatrix(sinAngle, cosAngle, axis, Point3D.Origin);

            this.xAxis = xform * this.xAxis;
            this.yAxis = xform * this.yAxis;
            this.zAxis = xform * this.zAxis;

            this.UpdateEquation();
        }
        else
        {
            // 지정된 pivot을 기준으로 평면 전체(원점/축/방정식)를 변환
            xform.UpdateMatrix(sinAngle, cosAngle, axis, pivot);
            this.TransformBy(xform);
        }
    }


    public void Rotate(double angleInRadians, Vector3D axis)
    {
      this.Rotate(angleInRadians, axis, Point3D.Origin);
    }

    public void Rotate(double angleInRadians, Vector3D axis, Point3D center)
    {
      this.Transform(Math.Sin(angleInRadians), Math.Cos(angleInRadians), axis, center);
    }

    public void Translate(double dx, double dy, double dz = 0.0)
    {
      Transformation xform = new Transformation();
      xform.Translation(dx, dy, dz);
      this.TransformBy(xform);
    }

    public void Translate(Vector3D delta)
    {
      Transformation xform = new Transformation();
      xform.Translation(delta);
      this.TransformBy(xform);
    }


    public static Plane XY => new Plane();

    public static Plane YX => new Plane(Point3D.Origin, Vector3D.AxisY, Vector3D.AxisX);


    public static Plane YZ => new Plane(Point3D.Origin, Vector3D.AxisY, Vector3D.AxisZ);


    public static Plane ZY => new Plane(Point3D.Origin, Vector3D.AxisZ, Vector3D.AxisY);


    public static Plane ZX => new Plane(Point3D.Origin, Vector3D.AxisZ, Vector3D.AxisX);


    public static Plane XZ => new Plane(Point3D.Origin, Vector3D.AxisX, Vector3D.AxisZ);

    public double DistanceTo(Point3D point) => Vector3D.Dot(point - this.origin, this.zAxis);

    public bool Flip()
    {
      Vector3D xAxis = this.xAxis;
      this.xAxis = this.yAxis;
      this.yAxis = xAxis;
      this.zAxis.Negate();
      this.UpdateEquation();
      return true;
    }

    public void Project(Point3D P, out double s, out double t)
    {
      Vector3D vector3D = Vector3D.Subtract(P, this.origin);
      s = vector3D * this.xAxis;
      t = vector3D * this.yAxis;
    }

    public Point2D Project(Point3D P)
    {
      double s;
      double t;
      this.Project(P, out s, out t);
      return new Point2D(s, t);
    }

    public Vector2D Project(Vector3D P)
    {
      double s;
      double t;
      this.Project(P.AsPoint, out s, out t);
      return new Vector2D(s, t);
    }


    public Point3D Origin
    {
      get => this.origin;
      set
      {
        this.origin = value;
        this.UpdateEquation();
      }
    }


    public Vector3D AxisX => this.xAxis;


    public Vector3D AxisY => this.yAxis;


    public Vector3D AxisZ
    {
      get => this.zAxis;
      set
      {
        this.zAxis = value;
        if (!this.SetOriginNormal((Point3D) this.Origin.Clone(), (Vector3D) value.Clone()))
          throw new ArgumentException("Invalid Z Axis");
      }
    }

    public PlaneEquation Equation => this.equation;

    public override string ToString()
    {
      return  this.origin?.ToString() +  this.zAxis?.ToString();
    }

    public bool Equals(Plane other)
    {
      if ((object) other == null)
        return false;
      if ((object) this == (object) other)
        return true;
      return object.Equals((object) other.zAxis, (object) this.zAxis) && object.Equals((object) other.yAxis, (object) this.yAxis) && object.Equals((object) other.xAxis, (object) this.xAxis) && object.Equals((object) other.origin, (object) this.origin);
    }

    public override bool Equals(object obj)
    {
      if (obj == null)
        return false;
      if ((object) this == obj)
        return true;
      return (object) (obj as Plane) != null && this.Equals((Plane) obj);
    }

    public override int GetHashCode()
    {
      return (((this.zAxis != (Vector3D) null ? this.zAxis.GetHashCode() : 0) * 397 ^ (this.yAxis != (Vector3D) null ? this.yAxis.GetHashCode() : 0)) * 397 ^ (this.xAxis != (Vector3D) null ? this.xAxis.GetHashCode() : 0)) * 397 ^ (this.origin != (Point3D) null ? this.origin.GetHashCode() : 0);
    }

    public static bool operator ==(Plane left, Plane right)
    {
      return object.Equals((object) left, (object) right);
    }

    public static bool operator !=(Plane left, Plane right)
    {
      return !object.Equals((object) left, (object) right);
    }

  }

  [Serializable]
  public class GPlanarEntity
  {
    private Plane _plane;

    protected GPlanarEntity() => this._plane = Plane.XY;

    public GPlanarEntity(Plane pln) => this._plane = pln;

    protected GPlanarEntity(GPlanarEntity another)
      : base((GEntity) another)
    {
      this._plane = (Plane) another._plane.Clone();
    }

    protected virtual void Update(GPlanarEntity another)
    {
      this._plane = (Plane) another._plane.Clone();
    }

    public override object Clone() => (object) new GPlanarEntity(this);

    public Plane Plane
    {
      get => this._plane;
      set
      {
        this._plane = value;
        this.ResetComputedData();
      }
    }

    public override void TransformBy(Transformation transform)
    {
      this._plane.TransformBy(transform);
      this.ResetComputedData();
    }

    public override bool IsValid(Point3D[] vertices = null)
    {
      return base.IsValid() && this._plane.IsValid();
    }

    public override Point3D[] GetCoarseSampling()
    {
      return new Point3D[1]{ this.Plane.Origin };
    }
  }
}
```