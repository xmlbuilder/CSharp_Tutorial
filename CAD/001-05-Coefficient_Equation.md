# Coefficient / Equation
```csharp
using System;
using System.Collections.Generic;
using System.Diagnostics;
using System.Globalization;

namespace Geometry
{
  /// <summary>
  /// Sparse vector/row에서 (열 인덱스, 값) 한 항(term)을 나타내는 불변 계수.
  /// 동치성은 Position(열 인덱스)만으로 판단합니다.
  /// </summary>
  public readonly struct Coefficient : IEquatable<Coefficient>, IComparable<Coefficient>
  {
    public int Position { get; }
    public double Value { get; }

    public Coefficient(int position, double value)
    {
      Position = position;
      Value = value;
    }

    /// <summary>Position만 비교(동일 열인가?)</summary>
    public bool Equals(Coefficient other) => Position == other.Position;
    public override bool Equals(object obj) => obj is Coefficient c && Equals(c);

    /// <summary>Position만으로 해시(Equals와 일관성 유지)</summary>
    public override int GetHashCode() => Position;

    /// <summary>Position 오름차순 정렬용 비교자</summary>
    public int CompareTo(Coefficient other) => Position.CompareTo(other.Position);

    public override string ToString()
      => string.Create(CultureInfo.InvariantCulture, $"{Position}, {Value}");
  }

  /// <summary>
  /// 희소 선형식(한 행 혹은 한 식) = Σ a_i * x_i
  /// 내부적으로 Position(열 인덱스) 오름차순 정렬을 유지합니다.
  /// </summary>
  public sealed class Equation
  {
    private const double ZeroTolerance = 1e-15;
    private readonly List<Coefficient> _terms;

    /// <summary>현재 항들의 읽기 전용 뷰</summary>
    public IReadOnlyList<Coefficient> Coefficients => _terms;

    public Equation() : this(capacity: 0) { }

    public Equation(int capacity)
    {
      _terms = capacity > 0 ? new List<Coefficient>(capacity)
                            : new List<Coefficient>();
    }

    /// <summary>
    /// 항 추가(동일 Position이 있으면 누적). 0에 가까운 계수는 자동 제거.
    /// </summary>
    public void Add(int position, double value)
    {
      if (double.IsNaN(value)) return;
      if (Math.Abs(value) <= ZeroTolerance) return;

      int idx = LowerBound(position);

      if (idx < _terms.Count && _terms[idx].Position == position)
      {
        double sum = _terms[idx].Value + value;
        if (Math.Abs(sum) <= ZeroTolerance)
          _terms.RemoveAt(idx);
        else
          _terms[idx] = new Coefficient(position, sum);
      }
      else
      {
        _terms.Insert(idx, new Coefficient(position, value));
      }
    }

    /// <summary>
    /// 변수 인덱스(position) 자체가 제거되었다고 가정하고,
    /// 해당 위치의 항은 삭제하고, 그보다 큰 모든 항의 Position을 1 감소시킵니다.
    /// (즉, 열 인덱스 재매핑)
    /// </summary>
    public void RemoveAt(int position)
    {
      int idx = LowerBound(position);

      // 해당 위치의 항이 존재하면 제거
      if (idx < _terms.Count && _terms[idx].Position == position)
      {
        _terms.RemoveAt(idx);
      }

      // position보다 큰 모든 항의 인덱스를 1 감소
      for (int i = idx; i < _terms.Count; i++)
      {
        var c = _terms[i];
        if (c.Position > position)
          _terms[i] = new Coefficient(c.Position - 1, c.Value);
      }
    }

    /// <summary>
    /// 해당 인덱스의 항을 반환. 없으면 Position = -1, Value = 0 으로 반환.
    /// (null/예외 대신 명시적 "없음" 신호)
    /// </summary>
    public Coefficient this[int position]
    {
      get
      {
        int idx = LowerBound(position);
        if (idx < _terms.Count && _terms[idx].Position == position)
          return _terms[idx];

        return new Coefficient(-1, 0.0);
      }
    }

    /// <summary>밀집 벡터 x와의 내적 Σ a_i * x[position_i]</summary>
    public double MultiplyBy(double[] x)
    {
      if (x == null) throw new ArgumentNullException(nameof(x));

      double sum = 0.0;
      for (int i = 0; i < _terms.Count; i++)
      {
        var c = _terms[i];
        // 인덱스 체크(디버그 빌드)
        Debug.Assert(c.Position >= 0 && c.Position < x.Length, "Index out of range in MultiplyBy(double[]).");
        sum += c.Value * x[c.Position];
      }
      return sum;
    }

    /// <summary>
    /// CSR(Compressed Sparse Row) 한 행과 밀집 벡터 x의 곱.
    /// I: 행 포인터(길이 = rowCount+1), J: 열 인덱스, A: 값
    /// </summary>
    public double MultiplyBy(int rowIndex, int[] I, List<int> J, List<double> A, double[] x)
    {
      if (I == null || J == null || A == null || x == null)
        throw new ArgumentNullException("CSR arguments must not be null.");

      int start = I[rowIndex];
      int end   = I[rowIndex + 1];

      double sum = 0.0;
      for (int k = start; k < end; k++)
      {
        int col = J[k];
        sum += A[k] * x[col];
      }
      return sum;
    }

    /// <summary>항 갯수(편의)</summary>
    public int Count => _terms.Count;

    public override string ToString() => $"Equation(Count={_terms.Count})";

    /// <summary>
    /// 정렬된 _terms에서 주어진 position이 들어갈 첫 인덱스(lower_bound).
    /// </summary>
    private int LowerBound(int position)
    {
      int lo = 0;
      int hi = _terms.Count;
      while (lo < hi)
      {
        int mid = (lo + hi) >> 1;
        if (_terms[mid].Position < position) lo = mid + 1;
        else hi = mid;
      }
      return lo;
    }
  }
}



```
