# Matrix
```csharp
using System;

namespace Geometry
{
  /// <summary>
  /// 2×2/3×3/4×4 행렬에 대한 소규모 선형대수 유틸리티.
  /// 주의: 3×3 관련 메서드는 전달된 배열의 좌상단 3×3 블록만 사용합니다(배열이 4×4여도 OK).
  /// </summary>
  public static class Matrix
  {
    private const double DetEpsilon = 1e-12;

    /// <summary>좌상단 3×3 블록의 행렬식</summary>
    public static double Determinant3(double[,] m)
    {
      if (m == null) throw new ArgumentNullException(nameof(m));
      // a = m[0,0], b = m[0,1], c = m[0,2] ...
      double a = m[0, 0], b = m[0, 1], c = m[0, 2];
      double d = m[1, 0], e = m[1, 1], f = m[1, 2];
      double g = m[2, 0], h = m[2, 1], i = m[2, 2];

      // a(ei − fh) − b(di − fg) + c(dh − eg)
      return a * (e * i - h * f)
           - b * (d * i - g * f)
           + c * (d * h - g * e);
    }

    /// <summary>좌상단 2×2 블록의 행렬식</summary>
    public static double Determinant2(double[,] m)
    {
      if (m == null) throw new ArgumentNullException(nameof(m));
      return m[0, 0] * m[1, 1] - m[0, 1] * m[1, 0];
    }

    /// <summary>
    /// 좌상단 3×3 블록의 역행렬을 계산하여 4×4로 돌려줍니다.
    /// (0..2,0..2)는 역행렬 값으로 채우고, 나머지는 영( [3,3]=1 )으로 설정합니다.
    /// </summary>
    public static bool Inverse3(double[,] m, out double[,] inv)
    {
      if (m == null) throw new ArgumentNullException(nameof(m));

      inv = new double[4, 4];
      double det = Determinant3(m);
      if (Math.Abs(det) < DetEpsilon) return false;

      // 코팩터(Adjugate) / det
      inv[0, 0] = (m[1, 1] * m[2, 2] - m[1, 2] * m[2, 1]) / det;
      inv[0, 1] = -(m[0, 1] * m[2, 2] - m[0, 2] * m[2, 1]) / det;
      inv[0, 2] = (m[0, 1] * m[1, 2] - m[0, 2] * m[1, 1]) / det;

      inv[1, 0] = -(m[1, 0] * m[2, 2] - m[1, 2] * m[2, 0]) / det;
      inv[1, 1] = (m[0, 0] * m[2, 2] - m[0, 2] * m[2, 0]) / det;
      inv[1, 2] = -(m[0, 0] * m[1, 2] - m[1, 0] * m[0, 2]) / det;

      inv[2, 0] = (m[1, 0] * m[2, 1] - m[1, 1] * m[2, 0]) / det;
      inv[2, 1] = -(m[0, 0] * m[2, 1] - m[0, 1] * m[2, 0]) / det;
      inv[2, 2] = (m[0, 0] * m[1, 1] - m[0, 1] * m[1, 0]) / det;

      // 나머지 채움
      inv[3, 3] = 1.0;
      return true;
    }

    /// <summary>
    /// (private) 4×4 행렬에서 (skipRow, skipCol)을 제거한 3×3 소행렬을 생성합니다.
    /// (기존 코드의 Transpose 기능과 이름만 다르고 동작은 동일)
    /// </summary>
    private static void BuildMinor3x3(double[,] source4x4, ref double[,] minor3x3, int skipRow, int skipCol)
    {
      if (minor3x3 == null || minor3x3.GetLength(0) != 3 || minor3x3.GetLength(1) != 3)
        minor3x3 = new double[3, 3];

      int rDst = 0;
      for (int r = 0; r < 4; r++)
      {
        if (r == skipRow) continue;
        int cDst = 0;
        for (int c = 0; c < 4; c++)
        {
          if (c == skipCol) continue;
          minor3x3[rDst, cDst++] = source4x4[r, c];
        }
        rDst++;
      }
    }

    /// <summary>
    /// (호환용) 기존 이름 유지: 4×4에서 (skipRow,skipCol) 제거한 3×3을 <paramref name="_param1"/>에 씁니다.
    /// </summary>
    private static void Transpose(double[,] _param0, ref double[,] _param1, int _param2, int _param3)
    {
      BuildMinor3x3(_param0, ref _param1, _param2, _param3);
    }

    /// <summary>4×4 행렬식</summary>
    public static double Determinant4(double[,] m)
    {
      if (m == null) throw new ArgumentNullException(nameof(m));

      double det = 0.0;
      double sign = 1.0;
      double[,] minor = new double[3, 3];

      for (int c = 0; c < 4; c++)
      {
        BuildMinor3x3(m, ref minor, 0, c);
        det += sign * m[0, c] * Determinant3(minor);
        sign = -sign;
      }
      return det;
    }

    /// <summary>
    /// 4×4 역행렬(Adjugate/Det) – 성공 시 <c>true</c>.
    /// </summary>
    public static bool Inverse4(double[,] m, out double[,] inv)
    {
      if (m == null) throw new ArgumentNullException(nameof(m));
      inv = new double[4, 4];

      double det = Determinant4(m);
      if (Math.Abs(det) < DetEpsilon) return false;

      double[,] minor = new double[3, 3];

      for (int r = 0; r < 4; r++)
      {
        for (int c = 0; c < 4; c++)
        {
          BuildMinor3x3(m, ref minor, r, c);
          double cof = Determinant3(minor);
          if (((r + c) & 1) == 1) cof = -cof;      // 부호
          inv[c, r] = cof / det;                  // 전치해서 배치(adjugateᵀ / det)
        }
      }
      return true;
    }

    /// <summary>
    /// 4×4 역행렬(전개식 기반, 고정 배치). 
    /// ⚠️ 특이행렬 검사를 하지 않습니다(원본 구현 호환). det≈0이면 결과가 불안정/NaN일 수 있습니다.
    /// </summary>
    public static double[,] Inverse4(double[,] a)
    {
      if (a == null) throw new ArgumentNullException(nameof(a));

      double A00A11 = a[0, 0] * a[1, 1] - a[1, 0] * a[0, 1];
      double A00A12 = a[0, 0] * a[1, 2] - a[1, 0] * a[0, 2];
      double A00A13 = a[0, 0] * a[1, 3] - a[1, 0] * a[0, 3];
      double A01A12 = a[0, 1] * a[1, 2] - a[1, 1] * a[0, 2];
      double A01A13 = a[0, 1] * a[1, 3] - a[1, 1] * a[0, 3];
      double A02A13 = a[0, 2] * a[1, 3] - a[1, 2] * a[0, 3];

      double A22A33 = a[2, 2] * a[3, 3] - a[3, 2] * a[2, 3];
      double A21A33 = a[2, 1] * a[3, 3] - a[3, 1] * a[2, 3];
      double A21A32 = a[2, 1] * a[3, 2] - a[3, 1] * a[2, 2];
      double A20A33 = a[2, 0] * a[3, 3] - a[3, 0] * a[2, 3];
      double A20A32 = a[2, 0] * a[3, 2] - a[3, 0] * a[2, 2];
      double A20A31 = a[2, 0] * a[3, 1] - a[3, 0] * a[2, 1];

      double invDet = 1.0 / (A00A11 * A22A33 - A00A12 * A21A33 + A00A13 * A21A32
                           + A01A12 * A20A33 - A01A13 * A20A32 + A02A13 * A20A31);

      return new double[4, 4]
      {
        {
          (a[1,1]*A22A33 - a[1,2]*A21A33 + a[1,3]*A21A32) * invDet,
          (-a[0,1]*A22A33 + a[0,2]*A21A33 - a[0,3]*A21A32) * invDet,
          (a[3,1]*A02A13 - a[3,2]*A01A13 + a[3,3]*A01A12) * invDet,
          (-a[2,1]*A02A13 + a[2,2]*A01A13 - a[2,3]*A01A12) * invDet
        },
        {
          (-a[1,0]*A22A33 + a[1,2]*A20A33 - a[1,3]*A20A32) * invDet,
          (a[0,0]*A22A33 - a[0,2]*A20A33 + a[0,3]*A20A32) * invDet,
          (-a[3,0]*A02A13 + a[3,2]*A00A13 - a[3,3]*A00A12) * invDet,
          (a[2,0]*A02A13 - a[2,2]*A00A13 + a[2,3]*A00A12) * invDet
        },
        {
          (a[1,0]*A21A33 - a[1,1]*A20A33 + a[1,3]*A20A31) * invDet,
          (-a[0,0]*A21A33 + a[0,1]*A20A33 - a[0,3]*A20A31) * invDet,
          (a[3,0]*A01A13 - a[3,1]*A00A13 + a[3,3]*A00A11) * invDet,
          (-a[2,0]*A01A13 + a[2,1]*A00A13 - a[2,3]*A00A11) * invDet
        },
        {
          (-a[1,0]*A21A32 + a[1,1]*A20A32 - a[1,2]*A20A31) * invDet,
          (a[0,0]*A21A32 - a[0,1]*A20A32 + a[0,2]*A20A31) * invDet,
          (-a[3,0]*A01A12 + a[3,1]*A00A12 - a[3,2]*A00A11) * invDet,
          (a[2,0]*A01A12 - a[2,1]*A00A12 + a[2,2]*A00A11) * invDet
        }
      };
    }
  }
}

```

